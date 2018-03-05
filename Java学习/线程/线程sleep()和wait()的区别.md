# 线程sleep()和wait()的区别

## 	sleep()

​	sleep()方法是线程类(Thread)的静态方法。让调用线程进入睡眠状态，让出执行机会给其它线程。等到休眠时间结束后，线程进去就绪状态和其它线程一起竞争cpu的执行时间

## 	wait()

​	wait()方法是Object类中定义的方法。调用wait()方法后，会释放对象锁，暂停当前的线程，需要别的线程执行notify/notifyAll才能够重新获得cpu执行时间。

实例

```java
public class Test {
    public static void main(String[] args ){
        new Thread(new Thread1()).start();
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(new Thread2()).start();
    }
    private static class Thread1 implements Runnable{

        @Override
        public void run() {
            synchronized (Test.class){
                System.out.println("enter thread1... ");
                System.out.println("thread2 is waiting");
            }
            try {
                //调用wait()方法，线程会放弃对象锁，进入等待此对象的等待锁定池
                Test.class.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("thread1 is going on...");
            System.out.println("thread1 is over");
        }
    }
    private static class Thread2 implements Runnable{

        @Override
        public void run() {
            synchronized (Test.class){
                System.out.println("enter thread2...");
                System.out.println("thread2 is sleep");
                //只有针对此对象调用notify()方法后本线程才进入对象锁定池准备获取对象锁进入运行状态。
                Test.class.notify();
                //如果我们把代码：TestD.class.notify();给注释掉，即TestD.class调用了wait()方法，但是没有调用notify()方法，则线程永远处于挂起状态。
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("thread2 is going on....");
                System.out.println("thread2 is over!!!");
            }
        }
    }
}

```

执行结果:

```
enter thread1...
thread1 is waiting...
enter thread2....
thread2 is sleep....
thread2 is going on....
thread2 is over!!!
```

