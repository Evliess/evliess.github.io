---
layout: index
title: "Themeleaf"
category: java
date: 2018-01-21 15:17:55
---


### 标准的表达式语法

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


### Using Texts

- th:text
- th:utext





