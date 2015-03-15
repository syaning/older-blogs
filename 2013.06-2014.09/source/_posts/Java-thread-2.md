title: Java多线程(二)
date: 2014-03-18 16:47:19
categories: Java
tags:
---
### 五、线程同步
Java中的线程同步主要有两种方法：synchronized关键字和synchronized语句块。

#### 1.synchronized关键字
```java
public synchronized void execute();
```
可以使用synchronized关键字来修饰一个方法，此时该方法叫做同步方法。synchronized其实就是一种加锁机制。对于实例方法，要给调用该方法的对象加锁；对于静态方法，给该类加锁。如果一个线程通过一个对象来调用同步的实例（或者动态）方法，首先会给该对象（或该类）加锁，然后执行方法，然后解锁。在解锁之前，另一个调用该对象（或该类）中方法的线程都将被阻塞，直到解锁。当同步方法执行完或者产生异常时，会自动释放锁。

#### 2.synchronized语句块
```java
synchronized(object){
    // do something
}
```
synchronized不仅可以给调用方法的对象或者类加锁，还可以给任何对象加锁。同步语句允许设置方法中的部分代码同步，而不必要求整个方法同步，增强了程序的并发能力。因此，synchronized方法是一种粗粒度的并发控制，某一时刻，只能有一个线程执行该synchronized方法；而synchronized块则是一种细粒度的并发控制，只会将块中的代码同步，位于方法内、synchronized块之外的其他代码是可以被多个线程同时访问到的。
<!-- more -->
#### 3.加锁同步
可以通过显式加锁来实现线程同步，一个锁是Lock接口的实例。ReentrantLock是实现Lock接口的互斥锁，可以使用指定的公平策略（参数为一个boolean值）来构造。真正地公平策略确保等待时间最长的线程首先获得锁，而假的公平策略给任意一个在等待的线程而无需考虑任何访问顺序。示例如下：
```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
 
public class Account extends Thread {
    private static Lock lock = new ReentrantLock();
    private int balance = 0;
 
    public int getBalance() {
        return balance;
    }
 
    public void deposit(int amount) {
        lock.lock();
        try {
            balance += amount;
        } finally {
            lock.unlock();
        }
    }
}
```

在lock()的调用后紧跟一个try-catch块并且在finally语句中释放锁是一个很好的习惯，这样我们可以确保锁被释放。通常使用synchronized方法或语句比使用互斥锁简单，不过使用互斥锁对同步具有状态的线程更加直观和灵活。

### 六、线程间通信
#### 1. 通过wait及notify机制可以实现进程间的通信
wait(), notify()及notifyAll()时定义在Object里面的final方法，因此被所有对象继承，但是不能覆盖。
- wait: wait方法使得当前线程必须要等待，等到另外一个线程调用notify或者notifyAll方法。当一个线程调用了wait方法后，会释放掉对象的锁，当有另外一个线程通知了它的时候，会重新获得锁的拥有权，恢复执行。要确保调用wait方法的时候拥有锁，即，wait方法的调用必须放在synchronized方法或synchronized块中。（sleep方法也会导致线程暂停执行，但线程在睡眠的过程中是不会释放掉对象的锁的）
- notify: notify方法会唤醒一个等待当前对象的锁的线程。如果多个线程在等待，它们中的一个将会选择被唤醒。这种选择是随意的，和具体实现有关。被唤醒的线程是不能被执行的，需要等到当前线程放弃这个对象的锁。被唤醒的线程将和其他线程以通常的方式进行竞争，来获得对象的锁。也就是说，被唤醒的线程并没有什么优先权，也没有什么劣势，对象的下一个线程还是需要通过一般性的竞争。wait和notify方法要求在调用时线程已经获得了对象的锁，因此对这两个方法的调用需要放在synchronized方法或synchronized块中。
- notifyAll: 与notify类似，只不过唤醒所有等待当前对象的锁的进程。

#### 2.显式加锁情况下使用await和signal机制
通过创建Condition对象来实现。在这种情况下，Lock代替了synchronized，Condition对象代替了监视器（即wait和notify机制中被锁住的对象）。可以通过Lock的newCondition方法来创建一个Condition实例，实际上一个锁可以使用该方法创建任意数目的Condition对象。示例如下：
```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
 
public class Account extends Thread {
    private static Lock lock = new ReentrantLock();
    private static Condition newDeposit = lock.newCondition();
    private int balance = 0;
 
    public void withdraw(int amount) {
        lock.lock();
        try {
            while (balance < amount)
                newDeposit.await();
            balance -= amount;
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
 
    public void deposit(int amount) {
        lock.lock();
        try {
            balance += amount;
            newDeposit.signalAll();
        } finally {
            lock.unlock();
        }
    }
}
```
由此可见，使用这种方法进行线程同步更加灵活，并且更加直观。