---
layout: index
title: "Java 三种代理模式的实现"
category: java
date: 2018-03-16 15:17:55
---

```
package com.test;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

interface PhoneImpl {
	public void makeCall();

	public void sendMessage(String message);
}

class Iphone implements PhoneImpl {
	@Override
	public void makeCall() {
		System.out.println("Make a call");
	}

	@Override
	public void sendMessage(String message) {
		System.out.println("Send message: " + message);
	}
}

/**
 * Simple Proxy
 * 优点: 在不改变现有代码的基础上，可以增加额外的逻辑
 * 缺点： 如果被代理对象实现好多接口，代理类比较难以维护，如果被代理类又任何接口的变更，代理类也要做相应的变更，具有耦合度
 */
class Assistant implements PhoneImpl {
	private PhoneImpl phoneImpl;

	public Assistant(PhoneImpl phoneImpl) {
		this.phoneImpl = phoneImpl;
	}

	@Override
	public void makeCall() {
		System.out.println("Hi, I am junior assistant...");
		this.phoneImpl.makeCall();

	}

	@Override
	public void sendMessage(String message) {
		System.out.println("Hi, I am junior assistant...");
		this.phoneImpl.sendMessage(message);
	}
}

/**
 * Dynamic Proxy
 * 优点: 相对于简单的代理，此种方法比较灵活
 * 缺点：如果被代理对象不是基于接口的编程，那么就不能使用动态代理
 */
class IntermediateAssistant implements InvocationHandler {
	private Object target;
	
	public IntermediateAssistant(Object target) {
		this.target = target;
	}

	public Object getProxyInstance() {
		return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("Hello, I am a IntermediateAssistant");
		method.invoke(target, args);
		return null;
	}
}

class XIAOMI {
	public void sendMessage(String message){
		System.out.println("Sent by XIAOMI: " + message);
	}
	
	public final void makeCall() {
		System.out.println("Called by XIAOMI");
	} 
}
/**
 * 
 * 优点：可以为没有实现接口的类实现代理
 * 缺点:不能代理final修饰的方法
 *
 */
class SeniorAssistant implements MethodInterceptor{
	private Object target;
	
	public SeniorAssistant(Object target) {
		this.target = target;
	}

	public Object getInstance() throws Throwable {
		Enhancer enhancer = new Enhancer();
		enhancer.setSuperclass(this.target.getClass());
		enhancer.setCallback(this); 
		return enhancer.create();
	}

	@Override
	public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
		System.out.println("Hi, I am a SeniorAssistant");
		proxy.invokeSuper(obj, args);
		return null;
	}
	
}
/**
 * asm-3.3.1.jar
 * cglib-2.2.jar
 */
public class ProxyDemo {
	public static void main(String[] args) throws Throwable {
		//Simple Proxy
		Assistant assistant = new Assistant(new Iphone());
		assistant.makeCall();
		assistant.sendMessage("hftd");
		System.out.println("----------并不华丽的分割线----------");

		//Dynamic Proxy
		PhoneImpl IntermediateAssistant = (PhoneImpl)new IntermediateAssistant(new Iphone()).getProxyInstance();
		IntermediateAssistant.makeCall();
		IntermediateAssistant.sendMessage("hftd");
		System.out.println("----------并不华丽的分割线----------");
		//Cglib Proxy
		XIAOMI seniorAssistant = (XIAOMI)new SeniorAssistant (new XIAOMI()).getInstance();
		seniorAssistant.makeCall();
		seniorAssistant.sendMessage("hftd");
	}
}

```






