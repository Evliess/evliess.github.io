---
layout: index
title: "Themeleaf"
category: java
date: 2018-01-21 15:17:55
---


# 标准的表达式语法

- 简单的表达式  
	- 变量表达式：${...}  
	- 选择变量表达式: \*{...}  
	*\*{...}用来取th:object="${...}"中的变量*  
	- 消息表达式: #{...}  
	- URL表达式：@{...}  
	- fragment表达式:~{...}  

- 字面值  
	- 文本: 'one', 'two', ...  
	- 数字: 0, 34, 3.0, 12.3, ...  
	- 布尔: true, false  
	- Null: null  
	- literal tokens one, sometext, main, ...  

- 文本运算  
	- \+  
	- \|The name is ${name}\|  

- 数学运算  
	- 二元操作符： +, -, *, /, %  
	- 一元操作符: -   

- 布尔运算  
	- and, or, !, not

- 比较运算  
	- <, >, >=, <= (gt, lt, ge, le), ==, != (eq, ne)  

- 条件运算  
	- if? then , (if)?(then):(else),  
	- 默认值: (value)?: (defaultvalur)  

- Special tokens:  
	- No-Operation: _  


# Using Texts  

- th:text
- th:utext

# 常用对象的工具类  

- #execInfo: information about the template being processed.  
- #messages: methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.  
- #uris: methods for escaping parts of URLs/URIs  
- #dates: methods for java.util.Date objects: formatting, component extraction, etc.  
- #calendars: analogous to #dates, but for java.util.Calendar objects.  
- #numbers: methods for formatting numeric objects.  
- #strings: methods for String objects: contains, startsWith, prepending/appending, etc.  
- #objects: methods for objects in general.  
- #bools: methods for boolean evaluation.  
- #arrays: methods for arrays.  
- #lists: methods for lists.  
- #sets: methods for sets.  
- #maps: methods for maps.  
- #aggregates: methods for creating aggregates on arrays or collections.  
- #ids: methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).  

# Fragment

th:include, th:insert  

# 条件表达式  
```
<tr th:class="${row.even}? 'even' : 'odd'">
  ...
</tr>
```

# 迭代表达式 (th：each)
```
<tr th:each="prod : ${prods}">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>
```
## 可以获取迭代过程中的一些状态
- The current iteration index, starting with 0. This is the **index** property.  
- The current iteration index, starting with 1. This is the **count** property.  
- The total amount of elements in the iterated variable. This is the **size** property.  
- The iter variable for each iteration. This is the **current** property.  
- Whether the current iteration is even or odd. These are the **even/odd** boolean properties.  
- Whether the current iteration is the first one. This is the **first** boolean property.  
- Whether the current iteration is the last one. This is the **last** boolean property.  

```
<tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd}? 'odd'">
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>
```

# 条件表达式
th:if, th:unless,th:switch  

对于th:switch语句，一旦其中的一个th:case满足条件，那么后续的所有th:case的值都将被计算为false,默认的语句用th:case="*"表示。  
```
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
  <p th:case="*">User is some other thing</p>
</div>
```

# Thymeleaf布局

## th:insert,  th:replace,th:include (从thymeleaf3.0开始，不推荐使用)  
"{templatename::selector}" ：templatename指的是html的名字，selector指的是fragment的名字(th:fragment="copy")
## 可以通过selector选择器使用th:fragment  

```
<div id="copy-section">
  &copy; 2011 The Good Thymes Virtual Grocery
</div>


<body>

  ...

  <div th:insert="~{footer :: #copy-section}"></div>
  
</body>
```

## th:insert和th:replace,th:include的不同点  
- th:insert is the simplest: it will simply insert the specified fragment as the body of its host tag.  
- th:replace actually replaces its host tag with the specified fragment.  
- th:include is similar to th:insert, but instead of inserting the fragment it only inserts the contents of this fragment.  


# 参考  
[Thymeleaf Doc](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#mixing-coupled-and-decoupled-logic)
