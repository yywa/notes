# Runnable和Thread

​	Thread类也是实现自Runnable接口。Runnable接口中定义了一个run()方法

```java
public interface Runnable {
    /**
     * 使用实现接口 Runnable 的对象创建一个线程时，启动该线程将导致在独立执行的线程中调用对象的 run 方法。
     */
    public abstract void run();
}
```

​	Thread类实现run()方法

```java
@Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }
```

​	这个target在Thread类中是这么定义的

```java
private Runnable target;
```

​	也就是说target是一个runnable对象。而它正是从Thread的构造里面传进来的

```java
public Thread(Runnable target) {
        init(null, target, "Thread-" + nextThreadNum(), 0);
    }
```

实现Runnable接口相对于Thread类有如下好处：

1. 避免单继承的局限，一个类可以同时实现多个接口。
2. 适合资源共享。

资源共享实例

```java
public class MyThread extends Thread {
    private int ticket = 5;
    public MyThread(String name){
        super(name);
    }
    @Override
    public void run(){
        for(int i = 0;i<5;i++){
            sale();
        }
    }
    public void sale(){
        if(this.ticket>0){
            System.out.println(Thread.currentThread().getName()
                    + "卖票：1张 " + this.ticket--);
        }
    }
    public static void main(String[] args){
        Thread thread1 = new MyThread("售票1");
        Thread thread2 = new MyThread("售票2");
        Thread thread3 = new MyThread("售票3");
        thread1.start();
        thread2.start();
        thread3.start();
    }
}
```

执行结果

```
售票2卖票：1张 5
售票1卖票：1张 5
售票2卖票：1张 4
售票3卖票：1张 5
售票2卖票：1张 3
售票1卖票：1张 4
售票2卖票：1张 2
售票3卖票：1张 4
售票2卖票：1张 1
售票1卖票：1张 3
售票3卖票：1张 3
售票3卖票：1张 2
售票1卖票：1张 2
售票3卖票：1张 1
售票1卖票：1张 1
```

​	从执行结果可以看出每个线程都有5张票，并没有实现资源共享。

```java
public class YywThread implements Runnable {
    private int ticket = 5;

    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            sale();
        }
    }

    //使用同步方法
    public void sale() {
        if (this.ticket > 0) {
            System.out.println(Thread.currentThread().getName()
                    + "卖票： " + this.ticket--);
        }
    }

    public static void main(String[] args) {
        YywThread mt = new YywThread();
        Thread thread1 = new Thread(mt, "售票口一");
        Thread thread2 = new Thread(mt, "售票口二");
        Thread thread3 = new Thread(mt, "售票口三");
        thread1.start();
        thread2.start();
        thread3.start();
    }
}
```

执行结果

```
售票口一卖票： 5
售票口二卖票： 5
售票口一卖票： 4
售票口二卖票： 3
售票口一卖票： 2
售票口二卖票： 1
```

​	从执行结果中可以看出，资源实现了共享，但出现一个问题，售票口一和售票口二同时卖了一张票。针对上面的情况，需要加同步关键字synchronized解决。同步关键字的使用方法有两种

##### 	1.同步代码块

```java
public class MyThread implements Runnable {  
    private int ticket = 5;  
  
    @Override  
    public void run() {  
        for (int i = 0; i < 5; i++) {  
            synchronized (this) {  
                sale();  
            }  
        }  
    }  
  
    //使用同步方法  
    public void sale() {  
        if (this.ticket > 0) {  
           try {
                Thread.sleep(100);    //休息200毫秒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()  
                    + "卖票： " + this.ticket--);  
        }  
    }  
  
    public static void main(String[] args) {  
        MyThread mt = new MyThread();  
        Thread thread1 = new Thread(mt, "售票口一");  
        Thread thread2 = new Thread(mt, "售票口二");  
        Thread thread3 = new Thread(mt, "售票口三");  
        thread1.start();  
        thread2.start();  
        thread3.start();  
    }  
}  
```

执行结果

```
售票口一卖票： 5
售票口三卖票： 4
售票口三卖票： 3
售票口三卖票： 2
售票口三卖票： 1
```

##### 	2.同步方法

```java
public class MyThread implements Runnable {
    private int ticket = 5;

    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            sale();
        }
    }

    //使用同步方法
    public synchronized void sale() {
        if (this.ticket > 0) {
            try {
                Thread.sleep(100);    //休息200毫秒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()
                    + "卖票： " + this.ticket--);
        }
    }

    public static void main(String[] args) {
        MyThread mt = new MyThread();
        Thread thread1 = new Thread(mt, "售票口一");
        Thread thread2 = new Thread(mt, "售票口二");
        Thread thread3 = new Thread(mt, "售票口三");
        thread1.start();
        thread2.start();
        thread3.start();
    }
}
```

执行结果

```
售票口一卖票： 5
售票口一卖票： 4
售票口三卖票： 3
售票口二卖票： 2
售票口二卖票： 1
```

​	上述过程中间代码都加了一段线程休眠，如果不加，会出现一个线程把票都卖完的情况。