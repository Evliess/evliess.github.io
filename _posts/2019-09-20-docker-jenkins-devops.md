---
layout: index
title: "Setup Jenkins in docker"
category: devops
date: 2019-09-20 12:17:55
---


//Step 1 pull image

```
docker pull jenkins/jenkins:lts
```

//Step 2 write dockerfile
---
FROM jenkins/jenkins:lts
# if we want to install via apt
USER root
RUN apt-get update -y && apt-get install -y libltdl-dev && rm -rf /var/lib/apt/lists/*
# drop back to the regular jenkins user - good practice
USER jenkins
---

```
docker build -t waitplay/jenkins:lst .

```

//Step 3
docker run -u root --rm -d -p 32772:8080 -p 50000:50000 -v /var/jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker -v /etc/localtime:/etc/localtime  -e JAVA_OPTS=-Duser.timezone=Asia/Shanghai --name jenkins waitplay/jenkins:lst

//Step 4 download jdk-8u221-linux-x64.tar.gz

//Step 5 download http://us.mirrors.quenda.co/apache/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz

//Step 6
mkdir -p /var/jenkins_home/globalEnvi
chown -R 1000:1000 jenkins_home

cd /var/jenkins_home/globalEnvi
tar -zxvf jdk-8u221-linux-x64.tar.gz
tar -zxvf apache-maven-3.6.2-bin.tar.gz


//Step7 Set Jenkins -> configuration -> add Environment variables

JAVA_HOME=/var/jenkins_home/globalEnvi/jdk1.8.0_221
CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$CLASSPATH
M2_HOME=/var/jenkins_home/globalEnvi/apache-maven-3.6.2
PATH=$JAVA_HOME/bin:$PATH

//Step8 Global Tool Configuration

- Maven Configuration

xxx/conf/setting.xml
- JDK
-MAVEN

//Step9 Test shell script
java -version
mvn -version
docker -v

docker stop jenkins
```

