---
layout: index
title: "Springboot Usage"
category: java
date: 2018-01-21 15:17:55
---

### Bootstrap Modal 遇到的问题  

> 当关闭modal的时候，清除modal上的数据  

```javascript
$("#uploadTestData").on('hidden.bs.modal' , function(e) {
	$(this).removeData();
	$("#input#file").val("")
});
```

> 当Modal显示的时候，给Modal上的元素绑定事件  

```javascript
$("#uploadTestData").on('shown.bs.modal' , function(e) {
    //
});

```

### Springboot 四种常用的传递参数的方式  

> @PathVariable  

```java
@RequestMapping(value="/{user}", method=RequestMethod.GET)
	public User getUser(@PathVariable Long user) {
		// ...
	}
```

> @RequestParam  

```java
// GET /pets?color=red
@RequestMapping(value="/pets", method=RequestMethod.GET)
	public User getUser(@RequestParam String color) {
		// ...
	}
```

> @MatrixVariable  

```java
// GET GET /pets/42;q=11;r=22
@RequestMapping(value="/cars", method=RequestMethod.GET)
	public void findPet(@PathVariable String petId, @MatrixVariable int q) {
    // petId == 42
    // q == 11
}
```

> @RequestHeader  

```java
//Given request with headers:
Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300

@GetMapping("/demo")
public void handle(
        @RequestHeader("Accept-Encoding") String encoding,
        @RequestHeader("Keep-Alive") long keepAlive) {
    //...
}
```

### Springboot FileUpload配置  
- 配置MultipartConfigElement  

```java
@Bean
  public MultipartConfigElement multipartConfigElement() {
    MultipartConfigFactory factory = new MultipartConfigFactory();
    return factory.createMultipartConfig();
  }

```

- 在profile中配置上传文件的大小  

```yml
spring.servlet.multipart.max-file-size: 50M
spring.servlet.multipart.max-request-size: 50M
```

- 可以在页面端限制上传文件的格式  

```html
//配置文件上传的格式是csv
<input type="file" accept=".csv,application/vnd.ms-excel" id="uploadfile"/>
```