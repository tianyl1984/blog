---
layout: post
title: 浅谈jdk中Semaphore的用法
description: 浅谈jdk中Semaphore的用法
category: java
---

Semaphore类位于java.util.concurrent包下，通过Semaphore可以很轻松的完成对信号量的控制，通过对信号量的控制可以达到对资源访问的控制，不过Semaphore类只是一个资源数量的抽象表示，并不负责管理资源本身，可能会有多个线程同时访问一个资源，需要做好同步处理。主要方法如下：

	public void acquire() throws InterruptedException {}

	public void release() {}

acquire方法用于获取一个资源访问许可，如果没有方法会阻塞，其构造方法中可以指定公平获取还是竞争获取；release用于释放一个许可。完整例子如下：

	import java.util.Random;
	import java.util.concurrent.Semaphore;

	public class SemaphoreDemo {

		public static void main(String[] args) {
			int resource = 4;
			Semaphore semaphore = new Semaphore(resource);
			System.out.println("开始分配任务。。。");
			for (int i = 0; i < resource + 3; i++) {
				new Worker(semaphore).start();
			}
		}

		static class Worker extends Thread {

			private Semaphore semaphore;

			public Worker(Semaphore semaphore) {
				this.semaphore = semaphore;
			}

			@Override
			public void run() {
				try {
					System.out.println("Thread:" + Thread.currentThread().getName() + " try to get resource ");
					semaphore.acquire();
					System.out.println("Thread:" + Thread.currentThread().getName() + " got resource and working ... ");
					Thread.sleep(3000 + new Random().nextInt(5000));
				} catch (InterruptedException e) {
					e.printStackTrace();
				} finally {
					semaphore.release();
					System.out.println("Thread:" + Thread.currentThread().getName() + " release resource");
				}
			}

		}

	}
