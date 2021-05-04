# Java多线程编程核心技术【笔记】

## 第一章 Java多线程技能

#### 使用多线程的场景？

1. 阻塞 多线程提高运行效率
2. 依赖 不相互依赖的线程AB异步执行可提高效率，依赖的业务AB执行可以不使用多线程

#### 如何使用多线程？

1. 继承Thread类，重写run方法，主线程中调用子类实例的start()方法

   ```Java
   public class MyThread extends Thread{
     @Override
     	public void run(){
         super.run();
         System.out.println("使用子类继承");
       }
   }
   ```

   ```html
   执行start()的顺序不代表执行run()的顺序
   ```

2. 实现Runnable接口，重写run方法，创建Runnable实例对象，使用含有该接口实现类对象为参数的`线程构造方法`创建线程，调用run()方法。

   ```java
   public class MyRunnable implements Runnable{
     @Override
     public void run(){
       System.out.println("使用接口实现");
     }
   }
   public class Run(){
     public static void main(){
       Runnable runnable = new MyRunnable(); //多态 父类引用指向子类对象
       Thread thread = new Thread(runnable);
       thread.start();
     }
   }
   ```

**由于Thread.java类也实现了Runnable接口，构造函数Thread(Runnable target)不仅可以传入Runnable接口对象，而且可以传入一个Thread类的对象**

如果有某些服务含有`静态成员变量`，并且是单例模式的，那么很有可能出现线程安全问题。可以理解为，

1、局部变量/形式参数A得到值，

2、传递值给`类变量`B，

1➡️2的过程中，可能穿插了其他线程的1➡️2的过程，那么该线程就把其他线程应该B的值给覆盖了，造成线程不安全问题。

**i++操作在进入println()之前发生的，==println()是线程安全的==。**

------

#### currentThread()方法

返回`代码段`正在被哪个`线程`调用，是Thread类的静态方法。

主程序中直接使用线程的run()方法，当作普通方法按序执行；使用线程的start()方法，启用线程。

------

#### isAlive()方法

测试线程是否处于活动状态 ，如果线程处于正在运行或者准备开始运行的状态，即为存活。

------

#### sleep(long millis)方法

让当前正在执行的线程(`this.currentThread()`)休眠制定的时间(毫秒 millis)，是Thread类的静态方法。

有可能出现==InterruptedException==异常，表示有可能被中断。

------

**sleep(long mills,int nanos)** 

在指定的毫秒数➕指定的纳秒数内让当前正在执行的线程休眠(暂停执行)

------

**StackTraceElement[] getStackTrace()方法**

返回一个表示该线程栈跟踪元素数组，返回方法调用的轨迹，栈结构。

------

**static void dumpStack()方法**

将当前线程的堆栈跟踪信息输出至标准错误流。

------

**static Map<Thread,StackTraceElement[]> getAllStackTraces()**

返回所有活动线程的堆栈跟踪的一个映射。映射键是线程，每个映射值是一个StackTraceElement数组，表示相应Thread的堆栈转储。

------

**getId()方法**

取得线程的唯一标识

------

#### 判断线程是否为停止状态

- **使用interrupt()方法**

判断是否中断的两个方法：

```java
public static boolean interrupted()
```

```java
public boolean isInterrupted()
```

**区别**：

this.interrupted()能测试**==当前线程==**是否已经是中断状态，执行后具有清除状态标志值为false的功能。(连续调用两次，第二次会返回false，会把第一次的true结果清除)

this.isInterrupted()能测试**==线程Thread对象==**是否已经是中断状态，不清除状态标志。

如果先启动线程，再调用线程的interrupt()方法，可以停止线程，但是那个线程会把当前run()方法完全执行完毕再退出。

如果使用线程中异常处理的方式，就可以直接进入异常处理，不会继续执行后续程序。

- **在sleep状态下停止线程**

不管调用顺序，interrupt()和sleep()方法碰到一起就回出现异常

1. sleep状态执行interrupt()方法会出现异常
2. 调用interrupt()方法给线程打上了中断的标记，再执行sleep()方法也会出现异常

- **使用stop()方法停止异常**

调用stop()方法时会抛出java.lang.ThreadDeath异常，一般此异常不需要显示捕捉。

使用stop（）释放锁，有可能造成数据不一致。

- **使用"return;"语句停止线程**

------

#### 暂停线程

使用suspend()方法暂停线程，使用resume()方法来恢复线程的执行。

使用方式与interrupt()相似，线程类对象去调用该方法，谁挂起，谁恢复。

在同步方法/同步块种使用suspend()，容易造成阻塞/死锁，一旦拿到了锁的线程被挂起且未被恢复，其他线程无法访问同一锁对象。

容易出现数据不完整的情况，即非线程安全的，在某一线程被挂起期间，若不加其他保护操作，该线程中的部分数据也许会不正确/不完整(受其他线程的影响)。

------

#### yield()方法

放弃当前的CPU资源，让其他任务去占用CPU执行时间。

------

#### 线程的优先级

```java
public final void setPriority(int newPriority)
```

优先级分1～10个等级，不在该范围内抛出IllegalArgumentExcpetion()异常，预置定义优先级如下：

```java
public final static int MIN_PRIORITY = 1;
public final static int NORM_PRIORITY = 5;
public final static int MAx_PRIORITY = 10;
```

- **线程优先级的继承性**

例如，A线程启动B线程，则B线程的优先级与A相同

例子：线程A的run()方法中调用线程B的run()方法，他们优先级相同；如果更改A的优先级，B的也对应更改。

- **优先级的规律性**

线程的优先级与代码执行顺序无关，CPU尽量将执行资源让给优先级比较高的线程。

例子：A、B线程，优先级不同(10和1)，先启A、后启B线程循环5次，结果优先级高的线程经常先执行但并非一定更先于优先级低的。

- **优先级的随机性**

线程优先级与输出顺序无关，这两者并没有依赖关系，具有不确定性，随机性。

例子：A、B线程，优先级不同(5和6)，优先级高的线程未必先完成任务。

------

守护线程

一种特殊的线程，当进程中不存在非守护线程了，守护线程自动销毁。守护Daemon线程的作用是为其他线程的运行提供便利的服务，常见的如GC。

```java
MyThread thread = new MyThread();
thread.setDaemon(true); //此时thread就是一个守护线程
```

并且要在thread执行start()方法之前执行方法，否则出现`IllegalThreadStateException`异常

## 第二章 对象及变量的并发访问

#### synchronized同步方法

synchronized声明方法与**访问控制修饰符**的前后顺序无关。

方法内部的变量具有私有特性，永远是线程安全的。(与方法共存亡)

两个线程同时访问同一个业务对象中的一个没有同步方法，设计到实例对象的成员变量时，可能出现安全问题。

解决方式：在方法前加上synchronized关键字，表示锁对象是该类的实例对象，如果多个线程，在run方法中，调用了同一个实例的方法，(子线程与业务类是==has-a==的关系，通过以业务类为参数的构造方法，传递同一个业务实例来创建线程对象)

```java
业务对象，是单例的
```

1. A线程先持有object对象的Lock锁，B线程可以以异步的方式调用object对象的非synchronized类型的方法
2. A线程先持有object对象的Lock锁，B线程如果在这时调用object对象中的synchronized类型的方法，则需要等待，也就是同步
3. 在方法声明处添加synchronized并不是锁方法，而是锁当前类的对象
4. Java中“锁”就是“对象”，“对象”可以被映射成“锁”，如果在X对象中使用了synchronized关键字声明非静态方法，则X对象就被当成锁。

------

#### synchronized锁重入

“可重入锁”是指自己可以再次获取自己内部锁。

一个线程获得某个对象锁，该对象锁还没有释放，当其再次想要获取这个对象锁时还可以获取。(调用类中其他synchronized方法，可以实现)

**锁重入也支持父子继承的环境**

子类方法中可以调用父类的同步方法

**重写方法不使用synchronized**

子类重写父类的同步方法但是不添加synchronized关键字则不是同步方法。

```java
public static boolean holdLock(Object obj)
  //当currentThread在指定的对象上保持锁定时返回true
```

------

#### synchronized同步语句块

synchronized同步代码块将任意对象作为锁

synchronized关键字与访问控制修饰符顺序无关

```java
synchronized(this) //锁对象是当前对象(实例) 作用与同步方法相当 同一把锁
```

也可以锁其他对象，不一定时this，此时根据锁的同异来判断是同步调用还是异步调用。

------

#### 静态同步方法与synchronized(class)

基本也是作用相当，锁对象都是==Class类的单例对象==。

每一个*.java文件对应Class类的实例都是一个，在内存中是单例的。

同步sys static方法，同步syn(Class)代码块，可以对类的所有实例对象起作用

```java
public class Service{
	public void printA(){
		synchronized (Service.class){
			//	代码块
		}
	}
  synchronized public static void printB(){
    //
  }
}
//所有的service实例都是同一把锁
```

如果代码块中synchronized接受的锁是**常量池**中的临时变量，那么这种形式出现的同步块指向同一把锁(同一个临时变量)

------

#### volatile关键字

三大特性：

1. 可见性 `B线程能马上看到A线程更改的数据`
2. 原子性 `不能保证` double long是原子的，针对用volatile声明的int i变量进行i++操作时非原子。
3. 重排序  `禁止代码重排序`

被volatile修饰的**变量**，线程在取值时从原来的向==工作内存==中取(多为寄存器，存放的是变量在主内存的拷贝)，变为向==主内存==中取值。

**可以使用Atomic原子类进行i++操作实现原子性**

关键字volatile之前的代码可以重排

关键字volatile之后的代码可以重排

但是其前不可排其后，其后不可排其前 也同样适用于被synchronized(this)修饰的代码执行情况

## 第三章 线程间通信

#### wait/notify机制

此锁就是厕所

<a id="wait"> wait()</a>

```java
//是Object类的方法
作用：使当前执行wait()方法的线程等待,状态由RUNNABLE➡️WAITING 
(直接离开厕所，让出厕所)
线程必须获得该对象的对象级别锁，只能在同步方法或同步块中调用wait()方法。
如果线程调用wait()方法时未持有锁，那么会报IllegalMonitorStateException异常，是运行时异常。
```

notify()

```java
作用：使处于等待锁的线程重新获得锁
执行notify()方法后，当前线程不会马上释放锁，呈wait状态的线程也并不能马上获取该对象锁，要等待notify()方法的线程程序执行完，即退出synchronized同步区域之后，线程才会释放锁。
(在厕所内把剩余的活干完，离开厕所，叫刚刚那个让出厕所的人回来上厕所)
线程必须获得该对象的对象级别锁，只能在同步方法或同步块中调用notify()方法。
如果线程调用notify()方法时未持有锁，那么会报IllegalMonitorStateException异常，是运行时异常。
```



wait() notify() notifyAll()是Object类的方法，锁对象可以调用。

suspend(),resume(),yield(),sleep(),stop(),interupt()是线程类的方法，线程对象调用...??

**哪些情况会进入运行状态？**RUNNABLE

1. sleep()到时
2. 处于阻塞的线程得到锁(可以执行同步方法/同步代码块)
3. 处于等待的线程被其他占用同步资源(锁)的线程唤醒
4. 从挂起状态(suspend())中恢复(resume())

**哪些情况会进入阻塞状态？BLOCKED**

1. 线程调用sleep()方法，主动放弃占有的处理器资源。离开厕所一小会，定个时，也可能一直离开了。
2. 线程调用了阻塞式I/O方法
3. 竞争锁资源
4. 等待通知...???
5. 挂起 suspend()

与nofify()有关的点

1. notify()唤醒，是按照进入wait()的顺序来的。(ReentranceLock却有非公平锁)
2. 同步代码块内遇到异常导致线程终止，锁也被释放

#### notifyAll()方法：通知所有线程

该方法会按照执行wait()方法的==倒序==依次对其他线程进行唤醒。

**wait(long)方法**

在该时间内处于WAITING状态，超过之后自动唤醒

自动唤醒的前提是仍然可以访问同步资源/持有锁，如果锁被其他线程占用，则要继续等待。(不用唤醒就能醒，但是要厕所没人才行)

------

#### 线程之间通信——字节流/字符流

管道流(pipe stream)是一种特殊的流，用于在不同线程之间直接传送数据。

```java
PipedeInputStream
PipedOutputStream
PipedReader
PipedWriter
//四个流有关的类实现线程之间的管道通信，读写字节/字符
//举例建立连接
PipedInputStream inputStream = new PipedInputStream();
PipedOutputStream outputStream = new PipedOutputStream();
inputStream.connect(outputStream);
outputStream.connect(inputStream);//与上一行方法等效
//字符流之间的读写类似
```

------

#### join()方法

作用：等待线程对象销毁

使所属的线程对象x正常执行run()方法中的任务，而使当前线程z进行无限期的阻塞，等待线程x销毁后再继续执行线程z后续代码，具有串联执行效果。

```java
public class ThreadA extends Thread{
  @Override
  public void run(){
	Thread threadB = new Thread(){
    @Override
    public void run(){
      System.out.println("线程B正在执行...");
      Thread.sleep(6000);
    }
  };
    threadB.start();
    threadB.join();
    System.out.println("线程A正在执行...");
  }
}

```

先打印B，再打印A。因为线程A被阻塞了。谁的run()方法中发生了其他线程的join()方法，谁被阻塞。...对吗..?

**join()方法在内部使用wait()方法进行等待**

join()与interrupt()方法如果彼此遇到，则出现异常，不管先后顺序。这里是线程A被线程B启动了并且A调用了join()方法后(线程B被阻塞)，如果线程B在其他线程中使用了interrupt()方法，那么会报InterruptedException()异常。

**join(long)**

设定等待的时间，不管主调方线程是否执行完毕，时间到了并且重新获得锁之后，当前线程可以继续向后执行。

join(long)与sleep(long)的异同

一个是进入waiting状态，一个进入blocked状态。即释放锁和不释放锁的区别。

#### ThreadLocal类

使每一个线程拥有一个自己的变量(对象)。

通俗用法：在一个线程内创建一个ThreadLocal类的对象，使用其get()和set()方法来读取和保存独有的东西。

因为ThreadLocal类和Thread类在同一个java.lang包目录下（默认包级访问），ThreadLocal类可以访问Thread类中的成员变量ThreadLocalMap\<ThreadLocal<?>,Object>。所有的线程都有这样一个Map，而ThreadLocal读写本质就是，ThreadLocal对象(tlObj)获取该线程的Map，通过以tlObj为键去读写，如果没有对应的键值对，则会创建并且保存。

![image-20210328130547685](/Users/jone/Library/Application Support/typora-user-images/image-20210328130547685.png)

这个图还是很直观的说明了ThreadLocal类与Thread类之间的依存关系。

**重写initialValue()方法**

如果首次get()，即ThreadLocal对象还没被保存进Map过，返回是空。可以通过重写ThreadLocal的initialValue()的方法来设定键为空时的初始值。

```java
public class ThreadLocalExt extends ThreadLocal{
  @Override
  protected Object initialValue(){
    return "首次调用get(),返回的就是我这几个字，不是null";
  }
}
```

从某一个线程A中另外开启一个线程B(A的子线程)，线程B无法通过同一个ThreadLocal对象活动相同的值。

但是使用InheritableThreadLocal类可以让子线程从父线程继承值。只不过是调用了Thread类中的ThreadLocalMap型的inheritableThreadLocals成员变量罢了，在子类线程首次调用get()时，虽然没有存Entry，但是不会像上面那样返回初始值，而是会创建一个父类线程存放内容的拷贝，这样可以直接拿那个值。

就不看源码了.. 知道这个意思就行。

如果父类线程存的是一个复合类变量，比如User类，那么子类线程读取之后对该User对象进行修改操作，父类线程也能感知得到，因为存的是同一个对象的引用，传递的也是。

通过重写childValue()方法，可以在拷贝的时候对父类存的内容进行一些修改(原来是直接搬走的)

```java
public class InheritableThreadLocalExt extends InheritableThreadLocal{
  @Override
  protected Object childValue(){
    return parentValue + "，子类get()时的额外内容";//这里默认存的是字符串，如果不是不一定好用
  }
}
```

## 第四章 Lock对象的使用

#### 使用ReentrantLock类

使用方法：调用ReentrantLock对象的Lock()方法获取锁，调用unlock()方法释放锁，这两个方法成对使用。想要实现同步某些代码，把这些代码放在lock()和unlock()之间。

书上的例子是，只创建了一个Service对象，里面有一个ReentrantLock()对象，所有的线程与Service是has-a的关系，在线程创建的时候调用其构造函数，传递的是同一个Service对象，使用的是==同一把锁==。

```java
private Lock lock = new ReentrantLock();
public void testMethod(){
  lock.lock();
  //同步执行的代码
  lock.unlock();
}
```

#### Condition类

Condition类对象的作用是控制并处理线程的状态，可使线程waiting，也可使其恢复runnable

Condition类来自jdk1.5，Lock对象可以创建多个Condition实例，线程对象注册在指定的Condition中，从而可以有选择性地进行线程通知，在调度上更灵活。



```java
public class MyService{
	private Lock lock = new ReentrantLock();
	private Condition condition = lock.newCondition();
	
	public void await(){
	try{
    lock.lock();
		condition.await();  //要在同步范围内使用condition.await()方法，如果不在该范围内会报异常
    //其作用和Object类的wait()方法一样
	}catch(InterruptedException e){
		e.printStackTrace();
	}finally{
    lock.unlock();
  }
	}	
  
	public void signal(){
	try{
    lock.lock();
		condition.signal();  
    //其作用和Object类的notify()方法一样
	}finally{
    lock.unlock();
  }
	}
}
```

```
Object类中的notifyAll()方法相当于Condition类中的signalAll()方法
```

**await()**原理

并发包源代码内部执行Unsafe类的

```java
public native void park(boolean isAbsolute,long time)
```

让当前线程暂停。isAbsolute表示是否为绝对时间，为真time是毫秒，为假time是纳秒。

(==|| 傻子吧..说半天也没说park怎么做的.. 例子就调用了一个反射来看看功能...源码也不粘一下，真能划水这屌作者...虽然我也是买的盗版嘻嘻..)

Condition对象可以唤醒指定种类的线程。可以由同一个lock创建不同的Condition对象，由哪个condition对象await()方法作用而陷入等待状态，就要由这个condition对象去signal()起来，其他condition对象无法signal()。就算是signalAll()，也是作用于同一个condition()等待的线程。

(..应该是吧..)

## 第五章 定时器Timer

TimerTask类主要作用是封装任务，其定义也是一个Runnable接口的实现类，所以也要重写run()方法。

使用Timer类来定时执行线程

```java
MyTask task = new MyTask extends TimerTask(){
  @Override
  public void run(){
		//run方法
  }
};
Timer timer = new Timer();
timer.schedule(task,new Date(System.currentTimeMillis() + 10000));//10s后执行任务线程
```

```java
public void schedule(TimerTask task,Date time)
```

```java
public void cancel()
//Timer类的方法， 终止次计时器，丢弃所有当前已经安排的任务（将任务队列中的全部任务清空），但并不会干扰当前正在执行的任务，
//在某计时器调用的计时器任务的run()方法内调用cancel()方法，可以确保正在执行的任务是此计时器所执行的最后一个任务。
```

如果计划时间早于当前时间，则立即运行任务。

```java
//TimeTask类也有cancel()方法，在run()中调用。

public void schedule(TimerTask task,Date firstTime,long period);
//第三个参数是周期任务的周期 单位应该是毫秒

//schedule有很多重载方法 不想一一枚举 应随设计思路不同而转变
```

被安排的任务，先进队列会先执行，如果在日程上的任务已经到时了，会等待队列前面的任务先执行完毕后才能得到cpu资源继续执行。相当于任务开始时间已经过去了，会立刻执行。

