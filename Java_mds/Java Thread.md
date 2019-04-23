# Java Thread

### What is Thread?

​	In the definition of Baidu Encyclopedia,thread  is the smallest unit that the operating system can schedule operations. It is included in the process and is the actual unit of operation in the process.The reason why we need thread is that threads can be created and managed less operating system overhead and fewer system resources and a process need more system resources.

​	And how does a thread works?

​	When you new a thread, a thread was created by OS and then pushed into ready queue.The the state of this thread is in runnable. And When OS need it work, it start work and in a running state. And beteen it in runnable and running, there have some state a thread may go through.And when its task finally ended, it is in a dead state, and OS will release its resources.

​	The working machanism of thread may like this:

![1554507586590](..\img_lib\1554507586590.png)

​	And more than this, a thread may have another state, and the working machansim of it is complex than the grapy display to you. And if you want to know it in detail , you can [see this](https://baike.baidu.com/item/线程/103101?fromtitle=Thread&fromid=5156974&fr=aladdin).

​	And now let's get into today's theme.



---

### Creating your thread!

​	There is two ways you can create a thread.

​	One way is create you own thread class which extends thread. Second way is to create a class whicl implements runnable.The first one may like this:

```java
public class myThread extends thread{
    private volatile boolean flag;
    myThread(){
        //make the flag ture
    }
    public void run(){
        //running methods
        while(flag)
        {...}//if the flag was true,the method can run
    }
    public void stop()
    {
        //make the flag false;
    }
}
main method{
    myThread().start();
}
```

​	The second way is like this:

```java
public class myThread implements Runnable{
    public void run()
    {....}
    ...
}
main method{
    new thread(new myThread()).start();
}
```

​	Actually, the class thread implements Runnable;

​	There's four ways for you to stop the thread.

- First , thread dead with a process's termination

- Second, use a varible flag to stop the while loop in the run method. Just like what you see above.

- Third,use interrupte(); This way is similar to the second way.

  ```java
  public void run()
  {
      while(!isInterrupted())
      {
          try{
              //Do something here
          }catch(InterruptedException e)
          {
              e.printStackTrace();
              break;
          }
      }
  }
  ```

  And if you use threadname.interrupted() method, the interrupted flag will turn to true. Then will throw an InterruptedException.

- And last way to terminate thread is use stop method. But it's unsaft when you call it will  to unlock all of the monitors that it has locked (describe in Java doc).



---

### Daemon 

​	What's Daemon? In Baidu Translate , it was describe as a creature in stories from ancient Greece that is half man and half god . In computer science, it is a thread which do something like a  patron saint is the end front of computer. It always process other front-end thread, to server them.

​	And why can't we use a  ordinary thread to be as a Daemon?  There's no  "can" or "can't", but it's very complex for us to do like this.

​	And What things can a daemon do?

​	A daemon may not do the main job, but it's important in some case. Such as the garbage collector, it always do not do any caculating work, but it can release the resources. Not only this, but also it can write journey , activity of other thread.



---

### Callable & Future

​	Callable is a interface like Runnable,the defination of Callable is like this:

```java
public interface Callable<V> {
    V call() throws Exception;
}
```

​	You can think it is a run() method but can return a V value.

​	And what's Future?It always use for cancelling the execution results of specific Runnable or Callable tasks, quering whether it is completed, and obtain the results.

​	The defination of it is like this:

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

​	Always, we need an ExecutorSevice instance , and use its submit() method to get a Feture instance.

​	There is a small example:

```java
public class CallableTest {
    public static final int taskSize =10;
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService pool = Executors.newFixedThreadPool(taskSize);
        List<Future> list = new ArrayList<Future>();
        for (int i = 0; i < taskSize; i++) {
            Callable c = new MyCallable(i);
            Future f = pool.submit(c);
            list.add(f);
        }
        pool.shutdown();
        for (Future f : list)
        {
            System.out.println("res：" + f.get().toString());
        }
    }
}
```

​	The MyCallable:

```java
class MyCallable implements Callable<String>
{
    private double CaculateResult;
    private int i;
    MyCallable(int i) {
        this.i=i;
    }
    @Override
    public String call() throws Exception {
        CaculateResult = i*i*4+645;
        return "The "+i+" MyCallable's result is"+CaculateResult;
    }
}
```

​	The result :

![1554728207674](..\img_lib\1554728207674.png)

​	And what's more, the Future is a interface ,  and you can not be instantiated. So, in this case, if you want to have some instance, you can use FutureTask, which realize the RunnableFuture interface.

​	And you just replace the code below in the for-loop:

```java
for(...)
{
    FutureTask<String> futuretask = new FutureTask(new MyCallable(i));
    pool.execute(futuretask);
    futureTaskList.add(futuretask);
}
```

The UML graph can describe in detail.

![Future[1]](..\img_lib\Future[1].png)



---

### Thread Pool

​	To study a new thing, we always want to know how it make up, what's the component it have? The UML graph is a good tool to make us take cognizance of the structure of it.

​	![1554811266131](..\img_lib\1554811266131.png)

​	The top of this machanism is a interface call Executor. And in the last, a class call Executors to realize most of the thing.

​	What we always use is ExecutorService,Executors, ThreadPoolExecutor. Use Executors's static method, we can create any kind of thread pool we want. And a table describe all  its static method.

| Return Type              | Method Name and Parameter                                    | Descriptions                                                 |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ExecutorService          | **newcachedThreadPool**()                                    | Creates a thread pool that creates new threads as needed, but will reuse previously constructed threads when they are |
| ExecutorService          | **newFixedThreadPool**(int nThreads)                         | Creates a thread pool that reuses a fixed number of threads operating off a shared unbounded queue. |
| ExecutorService          | **newSingleThreadExecutor**()                                | Creates an Executor that uses a single worker thread operating<br/>off an unbounded queue. |
| ScheduledExecutorService | **newScheduledThreadPool**(int  corePoolSize,<br/>hreadFactory threadFactory) | Creates a thread pool that can schedule commands to run after a<br/>given delay, or to execute periodically. |
| ScheduledExecutorService | **newSingleThreadScheduledExecutor**<br/>(ThreadFactorythreadFactory) | Creates a single-threaded executor that can schedule commands<br/>to run after a given delay, or to execute periodically. |

​	newCacheThreadPool Demo:

```java
public class CacheThreadPoolDemo{
    public static void main(String[] args)
    {
        ExecutorService pool = Executors.newCachedThreadPool();
        while(true)
        {
            if(MyRunnable.i>100)break;
            MyRunnable runnable = new MyRunnable();
            pool.execute(runnable);
        }
        pool.shutdown();
    }
}

class MyRunnable implements Runnable{

    public static volatile int i=0;
    @Override
    public void run() {
        i++;
        System.out.println("The i is"+i);
    }
}
```

​	Here, we create a CacheThreadPool. It do not have limit of thread number. So it is very suitable for improving the performance of programs that execute many short-lived asynchronous tasks. And it is very flexable.

​	The other Thread Pool have its own advantages. I have already describe in the table. And I will introduce its advance application in [this artical.]()



---

### ThreadPoolExecutor

​	If you want to create a ThreadPool accroding to your own design, and do not use the class Executors to create threadPool such as cacheThreadPool etc. And you can use the ThreadPoolExecutor.

​	To see the UML graph, you can see class Executors have an assciation relationship to the class ThreadPoolExecutor. And most of the methods in Executors are realizing by ThreadPoolExecutor. Now we see the souce code of Executors.

**CachedThreadPool**:

```java
public static ExecutorService newCachedThreadPool() {
     return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                   60L, TimeUnit.SECONDS,
                                   new SynchronousQueue<Runnable>());
}
```

​	Other ThreadPool are all constructed by the ThreadPoolExecutor's constructor but with a difference in parameters.

​	The constructor of ThreadPoolExecutor are like this:

ThreadPoolExecutor(int corePoolSize, int maximumPoolSize,long keepAliveTime, TimeUnit unit,
BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory, RejectedExecutionHandler handler)

​	Now we explain the parameters.

| Type                     | Paramether Names | Description                                                  |
| ------------------------ | ---------------- | ------------------------------------------------------------ |
| int                      | corePoolSize     | the number of threads to keep in the pool, even if they are idle, unless allowCoreThreadTimeOut is set |
| int                      | maximumPoolSize  | the maximum number of threads to allow in the pool           |
| long                     | keepAliveTime    | when the number of threads is greater than the core, this is the maximum time that excess idle threads  will wait for new tasks before terminating. |
| TimeUnit                 | unit             | the time unit for thekeepAliveTime argument                  |
| BlockingQueue<Runnable>  | workQueue        | the queue to use for holding tasks before they are executed.  This queue will hold only the Runnable  tasks submitted by the  execute method. |
| ThreadFactory            | threadFactory    | the factory to use when the executor creates a new thread    |
| RejectedExecutionHandler | handler          | the handler to use when execution is blocked because the thread bounds and queue capacities are reached |
​	And here is an example:

```java
public class ThreadTest {

    public static void main(String[] args) throws InterruptedException, IOException {
        int corePoolSize = 2;
        int maximumPoolSize = 4;
        long keepAliveTime = 10;
        TimeUnit unit = TimeUnit.SECONDS;
        BlockingQueue<Runnable> workQueue = new ArrayBlockingQueue<>(2);
        //Use a Bounded queue that limit the size of corePool
        ThreadFactory threadFactory = new NameTreadFactory();
        RejectedExecutionHandler handler = new MyIgnorePolicy();
        ThreadPoolExecutor executor = new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, unit,
                workQueue, threadFactory, handler);
        executor.prestartAllCoreThreads(); 
        
        for (int i = 1; i <= 10; i++) {
            MyTask task = new MyTask(String.valueOf(i));
            executor.execute(task);
        }

        System.in.read(); 
    }

    static class NameTreadFactory implements ThreadFactory {
	// a factory that create a linear name thread
    // override the newThread(Runnable r) to defind the way how to create a thread
    // when it was used in a threadpool, a threadpool will call the newThread method
    // to create a thread, and then the thread pool will contain it.
        private final AtomicInteger mThreadNum = new AtomicInteger(1);

        @Override
        public Thread newThread(Runnable r) {
            Thread t = new Thread(r, "my-thread-" + mThreadNum.getAndIncrement());
            System.out.println(t.getName() + " has been created");
            return t;
        }
    }

    public static class MyIgnorePolicy implements RejectedExecutionHandler
    /*
    *	Let this ThreadPool execute Reject statergy
    *	That is if the thread num > maximumPoolSize, it will let the other thread to wait
    *	else if the thread num >> maximumPoolSize, will reject the other thread 
    *	You can use the rejectedExecution to retrive a Runnable and a ThreadPoolExecutor
    *	And gather the information of it.
    *   It was just a handler, not do the most important thing in your code
    *   Just to process something not so important ,like logging
    * */
    {

        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            doLog(r, e);
        }

        private void doLog(Runnable r, ThreadPoolExecutor e) {
            // Do as a long
            System.err.println( r.toString() + " rejected");
        }
    }

    static class MyTask implements Runnable {
        private String name;

        public MyTask(String name) {
            this.name = name;
        }

        @Override
        public void run() {
            try {
                System.out.println(this.toString() + " is running!");
                Thread.sleep(3000); 
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        public String getName() {
            return name;
        }

        @Override
        public String toString() {
            return "MyTask [name=" + name + "]";
        }
    }
}
```

​	The machisiam of ThreadPool Executor is very complex. I will talk about how it work in detail in [this artical.]()

​	Thread is important point in Java, or I can say in any language. It is related to many kinds of knowledge. And the writer-- me, is not so good at it , and I wish you will read the artical in the reference carefully. And if you have find something wrong in my artical, or you have more in-deep understanding of it, I will you will contack to me.

​	Thank you for reading so much.



---

### Reference:

- [ideabuffer](http://www.ideabuffer.cn/2017/04/04/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Java%E7%BA%BF%E7%A8%8B%E6%B1%A0%EF%BC%9AThreadPoolExecutor/)
- [Java api doc](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html)
- [Jianshu blog](https://www.jianshu.com/p/f030aa5d7a28)
- Advance Java PDF(An summary artical, you can contack me to gain it.(Writing in Chinese) To write the propose in the mail , or I will ignore it!)

### Thanks for reading.

### Contack with me if there have problem:

​																		emial:271669603@qq.com

