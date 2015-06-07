---
layout: post
title:  "Hello Thread! (JAVA)"
date:   2015-06-04 15:30:06
categories: java console
---
In this post i describe how to implement threads in java. A thread is the basic unit of execution for any program. Threads are used to acheive concurrency in program execution. For instance, in a browser, two threads can be used to support streaming music and composing an email concurrently.

In java there are two idioms for creating threads. Extending the Thread class or implementing the Runnable interface.

The snippet below shows both implementations in one java console application:

{% highlight java %}

public class ChildThread extends Thread{
	public void run(){
		System.out.println("I am a child of the Thread class");
	}
}

public class RunnableImpl implements Runnable{
	@Override
	public void run(){
		System.out.println("I implement the runnable class");
	}
}

public class Main{
	public static void main(String[] args){
		new Thread(new RunnableImpl()).start();
		new ChildThread().start();
	}
}

{% endhighlight %}

Check out the [Java Docs][java-docs] on thread implementation for more details.



[java-docs]:  https://docs.oracle.com/javase/tutorial/essential/concurrency/runthread.html
