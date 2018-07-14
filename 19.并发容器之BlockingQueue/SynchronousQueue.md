# java阻塞队列之SynchronousQueue

SynchronousQueue，实际上它不是一个真正的队列，因为它不会为队列中元素维护存储空间。与其他队列不同的是，它维护一组线程，这些线程在等待着把元素加入或移出队列。和Go语言中的无缓冲区的Channel很相似。

因为SynchronousQueue没有存储功能，因此put和take会一直阻塞，直到有另一个线程已经准备好参与到交付过程中。仅当有足够多的消费者，并且总是有一个消费者准备好获取交付的工作时，才适合使用同步队列。

## example

```java
package com.jmz;

import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.TimeUnit;

public class syncQueue {
    public static void main(String[] args) {
        SynchronousQueue<Integer> queue = new SynchronousQueue<>();

        new Thread(() ->{
            System.out.println(System.currentTimeMillis() + ", Thread:" + Thread.currentThread() + " started...");
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(System.currentTimeMillis() + ", Thread:" + Thread.currentThread() + "started offering Integer 3 to SyncQueue.");
            try {
                queue.put(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(System.currentTimeMillis() + ", Thread:" + Thread.currentThread() + "complete offering Integer 3 to SyncQueue.");
        }).start();

        new Thread(()->{
            System.out.println(System.currentTimeMillis() + ", Thread:" + Thread.currentThread() + " started...");
            try {
                TimeUnit.SECONDS.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(System.currentTimeMillis() + ", Thread:" + Thread.currentThread() + "started polling from SyncQueue.");
            Integer i = null;
            try {
                i = queue.take();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(System.currentTimeMillis() + ", Thread:" + Thread.currentThread() + "complete polling Integer "+i+" from SyncQueue.");
        }).start();
    }
}

```

输出：

```
1531563045797, Thread:Thread[Thread-0,5,main] started...
1531563045798, Thread:Thread[Thread-1,5,main] started...
1531563047800, Thread:Thread[Thread-0,5,main]started offering Integer 3 to SyncQueue.
1531563055802, Thread:Thread[Thread-1,5,main]started polling from SyncQueue.
1531563055802, Thread:Thread[Thread-1,5,main]complete polling Integer 3 from SyncQueue.
1531563055803, Thread:Thread[Thread-0,5,main]complete offering Integer 3 to SyncQueue.
```
