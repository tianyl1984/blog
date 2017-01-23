---
layout: post
title: 浅谈jdk中动态代理类的用法
description: 浅谈jdk中动态代理类的用法
category: java
---

### 简介
通过动态代理可以很方便的实现aop功能，java实现动态代理除了第三方的cglib外，还有jdk本身提供的Proxy类。Proxy类只能对接口中的方法实现动态代理，而cglib则比较灵活，可以对任意类实现代理。

### 使用步骤
1.实现java.lang.reflect.InvocationHandler接口，接口中invoke方法即代理之后的调用方法。
2.使用java.lang.reflect.Proxy类的newProxyInstance方法生成代理类。
3.将代理类转为指定的接口，调用其中的方法。

### 主要方法

InvocationHandler接口

	/**
	*proxy为代理的具体的类（接口）
	*mothod为要执行的方法
	*args为方法的参数
	**/
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable;

Proxy类

	/**
	*loader为加载代理类的ClassLoader
	*interfaces为代理类需要实现的接口
	**/
	public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler handler) throws IllegalArgumentException

### 完整例子

ProxyDemo类，调用

	/**
	 * jdk动态代理类的使用
	 * 
	 * @author tianyl
	 * 
	 */
	public class ProxyDemo {

		public static void main(String[] args) {
			// 创建handler，具体的实现在handler中
			DynicInvocationHandler handler = new DynicInvocationHandler();
			// 创建代理类
			Object obj = Proxy.newProxyInstance(ProxyDemo.class.getClassLoader(), new Class[] { IEchoService.class }, handler);
			IEchoService echoService = (IEchoService) obj;
			// 执行
			echoService.echo("aaaa");
		}

	}


DynicInvocationHandler类

	import java.lang.reflect.InvocationHandler;
	import java.lang.reflect.Method;

	public class DynicInvocationHandler implements InvocationHandler {

		@Override
		public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
			// 具体实现
			System.out.println("invoke method : " + method.getName());
			System.out.println("args[0]:" + args[0]);
			return null;
		}

	}