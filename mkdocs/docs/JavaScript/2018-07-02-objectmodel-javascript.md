---
layout: index
title: "Details of the object model"
category: javascript
date: 2018-07-02 12:17:55
---

# Details of the object model  

> JavaScript不是基于class，而是基于object的语言。由于这个原因，创建一个继承关系就变得不是特别显而易见。  

## The differences between class-based and object-based languange

1. 基于class的语言清楚的区分类与实例。*class* 就是某一类物体的抽象，包括属性，方法。*instance* 就是某个类一个具体的实例。

2. 基于object的语言，不区分 *class* 与 *instance* , 但是有原型对象( *prototypical object* )的概念。任何一个对象可以拥有自己的属性，也可以在runtime的时候创建自己的属性。任何一个对象A都可以作为另外一个对象B的prototype，在B对象中可以共享A的属性。  

3. JavaScript使用*Function*关键字定义一个类，通过使用*new*关键字创建一个新的对象。

4. Javascript使用*prototypical object* 实现继承。

5. 基于*class*的语言在创建类之后不能改变类的属性。基于*object*的语言可以在运行时为一个对象添加或者删除属性。如果为一个父类添加属性，子类也会拥有相同的属性。

## The employee example

![image](./images/js/objectmodel-01.png)  

### Simple constructors

```javascript
//define Employee

function Employee() {
  this.name = '';
  this.dept = 'general';
};

//define Manager

function Manager() {
  Employee.call(this);
  //或者使用以下形式
  //this.base = Employee;
  this.reports = [];
  this.dept = "sale";
  this.team = "QQ";
};
Manager.prototype = Object.create(Employee.prototype);
//或者使用以下形式
//Manager.prototype = new Employee;

var manager = new Manager;

var m = new Manager;

console.log(m.dept);   //sale
console.log(m.reports); // []
console.log(m.team); // QQ

```

### More flexible constructors

```javascript

function Employee(name, dept) {
  this.name = name || '';
  this.dept = dept || 'general';
}

function WorkerBee(name, dept, projs) {
  //指定父类
  this.base = Employee;
  //调用父类构造方法
  this.base(name, dept);
  this.projects = projs || [];
}
WorkerBee.prototype = new Employee;

function Engineer(name, project, mach) {
  this.base = WorkerBee;
  this.base(name, 'engineering', project);
  this.dept = 'engineering';
  this.machine = mach || '';
}
Engineer.prototype = new WorkerBee;

var engine = new Engineer('Jack', ['python', 'perl'], 'belau');

console.log(engine.name, engine.dept, engine.projects, engine.machine); //Jack engineering [ 'python', 'perl' ] belau

```

### Prototype Chain

```javascript
//原型链就是对象的继承链，查找属性的时候沿着这个链条向上查找
var chris = new Engineer('Jack', ['python', 'perl'], 'belau');

// engine的原型链如下:

chris.__proto__ == Engineer.prototype;
chris.__proto__.__proto__ == WorkerBee.prototype;
chris.__proto__.__proto__.__proto__ == Employee.prototype;
chris.__proto__.__proto__.__proto__.__proto__ == Object.prototype;
chris.__proto__.__proto__.__proto__.__proto__.__proto__ == null;

//instance of 函数

function instanceOf(object, constructor) {
   object = object.__proto__;
   while (object != null) {
      if (object == constructor.prototype)
         return true;
      if (typeof object == 'xml') {
        return constructor.prototype == XML.prototype;
      }
      object = object.__proto__;
   }
   return false;
}

instanceOf(chris, Engineer); //true
instanceOf(chris, WorkerBee);//true
instanceOf(chris, Employee);//true
instanceOf(chris, Object);//true

```

### Local or Inherited values  

> 如果需要在运行时修改一个对象的属性，并且想让这个修改影响到这个对象的子类，使用inherited 的值。反之，使用local值。

```javascript
function Employee(name, dept) {
  // name and dept are local values
  this.name = name || '';
  this.dept = dept || 'general';
}

// type is a inherited value
Employee.prototype.type = 'none';

```






