---
layout: index
title: "Springboot Usage"
category: java
date: 2018-01-21 15:17:55
---

# Springboot handbook


## How to add custom logic when login success

```java

// Implements the ApplicationListener
public class MyApplicationListener implements ApplicationListener<AuthenticationSuccessEvent> {

// Write your own logic
@Override
  public void onApplicationEvent(AuthenticationSuccessEvent event) {
  //TODO
  }

}

```

## How to custom a AuthenticationProvider 

```java

@Component
public class StaticAuthenticationProvider implements AuthenticationProvider {

  private static final Logger logger = LoggerFactory.getLogger(StaticAuthenticationProvider.class);

  @Autowired
  private MyUserDetailService userDetailService;

  // Override the authenticate function.
  @Override
  public Authentication authenticate(Authentication authentication)
      throws AuthenticationException {
    String name = authentication.getName();
    UserDetails userDetails = userDetailService.loadUserByUsername(name);
    if (Constants.IDA_ADMIN_USER_NAME.equals(name) && userDetails.isEnabled()) {
      UsernamePasswordAuthenticationToken result = new UsernamePasswordAuthenticationToken(
          userDetails, authentication.getCredentials(), userDetails.getAuthorities());
      result.setDetails(authentication.getDetails());
      return result;
    } else {
      return null;
    }
  }

  // Override the supports function
  @Override
  public boolean supports(Class<?> authentication) {
    return authentication.equals(UsernamePasswordAuthenticationToken.class);
  }
}


```

## Spring Junit TestCase Clear Context after each test method

```java

//put the annotation to class level
@DirtiesContext(classMode = ClassMode.AFTER_EACH_TEST_METHOD)

```

## Springboot mockMvc 使用HttpSession

```java
MockHttpSession httpSession = new MockHttpSession();
httpSession.setAttribute();

MvcResult result = mockMvc.perform(
        put("/aaa/bbb").contentType(MediaType.APPLICATION_JSON_UTF8).session(httpSession)
            .content(json))
        .andExpect(status().is2xxSuccessful()).andReturn();

```

## Springboot 使用 @RequestBody 接受 Ajax Body中的复杂类型参数

```java

//定义Controller中的@RequestBody的参数类型
public class User {
  private String name;
  private List<String> cars;
  ...
}

//Js中的Ajax
var data = {
  "cars": ["car1", "car2"]，
  "name": "tom"
};

$.ajax({
  url: "xxx",
  dataType: "json",
  data: JSON.stringify(data),
  contentType: "application/json",
  headers: {
    Accept: "application/json"
  },
  success: function(){}
});

//3 Controller中取得Ajax Body中的数据

public String test(HttpSession httpSession,
      @RequestBody User payload) {
        payload.getCars();
}

```

## Bootstrap Modal 遇到的问题  

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

## Springboot 四种常用的传递参数的方式  

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

## Springboot FileUpload配置  
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

## Springboot with Docker

### 1. Write a docker file:
```shell
FROM java:8u111-jdk-alpine
COPY spring-cloud-eureka-0.0.1-SNAPSHOT.jar /usr/src/myapp/app.jar
WORKDIR /usr/src/myapp
CMD ["java", "-jar", "app.jar"]
```

### 2. Build an image
```shell
docker build -f dockerfile -t waitplay/myImage:latest
```

### 3. Run the image
```shell
//-e option 设置运行时环境变量
docker run -dt --name waitplay-server-1 -e "spring.profiles.active=srv1" -p 8761:8761 waitplay/eureka-server
```

