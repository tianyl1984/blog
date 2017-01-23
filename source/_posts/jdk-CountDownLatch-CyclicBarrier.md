---
layout: post
title: 浅谈jdk中CyclicBarrier和CountDownLatch的用法
description: 浅谈jdk中CyclicBarrier和CountDownLatch的用法
category: java
---

CountDownLatch位于java.util.concurrent包下，是一个同步的辅助类，允许一个或多个线程等待直到其他线程完成操作。主要方法如下：

	public void await() throws InterruptedException {}

	public void countDown() {}

await方法是进行等待，countDown方法将计数减1，await是阻塞操作，计数不为0时一直阻塞，计数值由构造器传入。看下面完整例子：

	package com.hzth.myapp.thread;

	import java.util.Random;
	import java.util.concurrent.CountDownLatch;

	public class CountDownLatchDemo {

		public static void main(String[] args) throws InterruptedException {
			int jobs = 3;
			CountDownLatch cdl = new CountDownLatch(jobs);
			System.out.println("开始分配任务。。。");
			for (int i = 0; i < jobs; i++) {
				new Worker(cdl).start();
			}
			System.out.println("等待完成任务。。。");
			// 等待计数为0
			cdl.await();
			System.out.println("任务已经全部完成。。。");
		}

		static class Worker extends Thread {

			CountDownLatch cdl = null;

			public Worker(CountDownLatch cdl) {
				this.cdl = cdl;
			}

			public void run() {
				System.out.println("Thread:" + Thread.currentThread().getName() + " is doing job ... ");
				try {
					Thread.sleep(3000 + new Random().nextInt(5000));
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println("Thread:" + Thread.currentThread().getName() + " finished job ");
				// 计数减1
				cdl.countDown();
			}

		}
	}

CyclicBarrier类提供的功能类似，像栅栏一样阻止通过，当全部到位之后才放行。主要方法如下：

	public int await() throws InterruptedException, BrokenBarrierException {}

await方法是一个阻塞的方法，只有await方法调用次数达到指定次数时才返回，指定次数在构造函数中指定，构造方法中的Runnable会在其他线程await返回之后执行，看下面完整的例子：


	package com.hzth.myapp.thread;

	import java.util.Random;
	import java.util.concurrent.BrokenBarrierException;
	import java.util.concurrent.CyclicBarrier;

	public class CyclicBarrierDemo {

		public static void main(String[] args) {
			int jobs = 4;
			CyclicBarrier cyclicBarrier = new CyclicBarrier(4, new Runnable() {
				@Override
				public void run() {
					System.out.println("所有任务已完成，开始退出。。。");
				}
			});
			System.out.println("开始分配任务。。。");
			for (int i = 0; i < jobs; i++) {
				new Worker(cyclicBarrier).start();
			}
			System.out.println("任务已分配完。。。");
		}

		static class Worker extends Thread {

			private CyclicBarrier cyclicBarrier;

			public Worker(CyclicBarrier cyclicBarrier) {
				this.cyclicBarrier = cyclicBarrier;
			}

			@Override
			public void run() {
				System.out.println("Thread:" + Thread.currentThread().getName() + " is doing job ... ");
				try {
					Thread.sleep(3000 + new Random().nextInt(5000));
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				System.out.println("Thread:" + Thread.currentThread().getName() + " finished job,wait other worker ... ");
				try {
					cyclicBarrier.await();
				} catch (InterruptedException | BrokenBarrierException e) {
					e.printStackTrace();
				}
				System.out.println("Thread:" + Thread.currentThread().getName() + " exit ... ");
			}

		}

	}


CountDownLatch和CyclicBarrier都能实现线程之间的等待，只是侧重点不同：
CountDownLatch侧重某个线程A等待其他线程执行完之后执行，CyclicBarrier侧重一组线程互相等待至某状态，再同时执行，另外CyclicBarrier可以重用。
