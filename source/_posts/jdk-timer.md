---
layout: post
title: TimerTask#run中抛出异常，导致任务终止
description: TimerTask#run中抛出异常，导致任务终止
category: java
---


在java项目开发中，经常需要用到计划任务，比较成熟的框架有Quartz。Quartz功能齐全，比较复杂，当有比较简单的定时任务是可以选择jdk提供的Timer类，Timer类使用简单，但是需要注意如果任务中抛出了异常，会出现什么情况？

Timer使用方法如下：


		Timer timer = new Timer();
		timer.schedule(new TimerTask() {
			@Override
			public void run() {
				System.out.println("Thread:" + Thread.currentThread().getName() + "job1:" + new Date());
				// do some other job
			}
		}, 1000, 1000);
		timer.schedule(new TimerTask() {
			@Override
			public void run() {
				System.out.println("Thread:" + Thread.currentThread().getName() + "job2:" + new Date());
				// do some other job
			}
		}, 1000, 2000);


timer中我们添加了两个定时触发的任务，希望任务按指定时间间隔触发，但是往往发现定时任务消失了，不再触发，一般是由于任务中抛出了异常，由于Timer只在单一线程中执行，一旦出现未捕获的异常，会导致整个线程退出，其他的定时任务也不再执行。解决方法很简单，只需要在run中使用try...catch捕获异常。

网上很多资料会推荐使用ScheduledThreadPoolExecutor类来做计划任务，它不仅可以保证精准的执行时间还可以在抛出异常之后不影响其他任务，使用方法如下：
	
	import java.util.Date;
	import java.util.concurrent.Executors;
	import java.util.concurrent.ScheduledExecutorService;
	import java.util.concurrent.TimeUnit;
	
	public class ScheduledThreadPoolExecutorDemo {
	
		public static void main(String[] args) {
			ScheduledExecutorService executor = Executors.newScheduledThreadPool(1);
			Runnable task1 = new Task1();
			Runnable task2 = new Task2();
			executor.scheduleAtFixedRate(task1, 1, 3, TimeUnit.SECONDS);
			executor.scheduleAtFixedRate(task2, 1, 5, TimeUnit.SECONDS);
		}
	}
	
	class Task1 implements Runnable {
	
		@Override
		public void run() {
			// 抛出异常，当前任务不再执行，定时触发的也一并取消，其他添加的继续执行
			System.out.println("Thread:" + Thread.currentThread().getName() + "\ttask1:" + new Date());
			throw new RuntimeException("stop");
		}
	
	}
	
	class Task2 implements Runnable {
	
		@Override
		public void run() {
			System.out.println("Thread:" + Thread.currentThread().getName() + "\ttask2:" + new Date());
		}
	
	}

注意：task1中抛出异常，task2不受影响继续执行，但是*task1的任务会取消，不再触发*，如果异常之后需要继续执行，还是需要try...catch。
