---
layout: index
title: "How nginx proxy docker api"
category: devops
date: 2018-11-21 15:17:55
---

# How nginx proxy docker api

1\. Vi openssl.cnf
``` 
[ v3_ca ]
basicConstraints = CA:true
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid
```

2\. Generate certification
```
[openssl](https://www.cnblogs.com/lsdb/p/9391979.html)
#CA证书及密钥生成方法一----直接生成CA密钥及其自签名证书
openssl req -newkey rsa:2048 -nodes -keyout ca-key.pem -x509 -days 365 -out ca.pem -subj "/C=ch/ST=ch/L=ch/O=ch/OU=ch/CN=9.30.94.86/emailAddress=test@test.com"

#服务器证书及密钥生成方法一----直接生成服务器密钥及待签名证书
openssl req -newkey rsa:2048 -nodes -keyout server-key.pem  -out server.csr -subj "/C=ch/ST=ch/L=ch/O=ch/OU=ch/CN=9.30.94.86/emailAddress=test@test.com"
#使用CA证书及密钥对服务器证书进行签名：
openssl x509 -req -days 365 -in server.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem -extfile ./openssl.cnf -extensions v3_ca

#客户端证书及密钥生成方法一----直接生成客户端密钥及待签名证书
openssl req -newkey rsa:2048 -nodes -keyout client-key.pem -out client.csr -subj "/C=ch/ST=ch/L=ch/O=ch/OU=ch/CN=9.30.94.86/emailAddress=test@test.com"
#使用CA证书及密钥对客户端证书进行签名
openssl x509 -req -days 365 -in client.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out client-cert.pem -extfile ./openssl.cnf -extensions v3_ca

```

3\. Verify vertification 
```
openssl x509 -in client-cert.pem -noout -purpose | grep SSL
openssl x509 -in server-cert.pem -noout -purpose | grep SSL
```

4\. vi ssl.conf for nginx
```
upstream docker-ssl {
  server unix:/var/run/docker.sock fail_timeout=0;
}
server {
    listen      443 ssl;
    server_name   localhost;
    ssl_certificate         /etc/nginx/certs/server-cert.pem;
    ssl_certificate_key     /etc/nginx/certs/server-key.pem;
    ssl_client_certificate  /etc/nginx/certs/ca.pem;
    ssl_verify_client              on;
#   ssl_verify_depth 3;

   location / {
      proxy_pass                 http://docker-ssl;
      proxy_redirect             off;

      proxy_set_header           Host             $host;
      proxy_set_header           X-Real-IP        $remote_addr;
      proxy_set_header           X-Forwarded-For  $proxy_add_x_forwarded_for;

      client_max_body_size       10m;
      client_body_buffer_size    128k;

      proxy_connect_timeout      90;
      proxy_send_timeout         120;
      proxy_read_timeout         120;

      proxy_buffer_size          4k;
      proxy_buffers              4 32k;
      proxy_busy_buffers_size    64k;
      proxy_temp_file_write_size 64k;
   }
}
```

5\. Vi dockerfile
```
FROM nginx
VOLUME ["/var/run", "/etc/nginx/certs"]
```

6\. Build image
```
docker rmi -f docker-api-nginx
docker build -t docker-api-nginx .
```

7\. Run with docker
```
docker run --rm --name docker-api-nginx -d -p 7443:443 -p 4080:80 \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /root/nginx-ca/ssl.conf:/etc/nginx/conf.d/ssl.conf \
-v /root/nginx-ca/certs:/etc/nginx/certs docker-api-nginx
```

8\. Used scripts
```
nginx -t -c /etc/nginx/nginx.conf
sudo systemctl restart nginx
tail /var/log/nginx/error.log 
usermod -a -G www-data,docker $USER
docker rmi -f docker-api-nginx
```