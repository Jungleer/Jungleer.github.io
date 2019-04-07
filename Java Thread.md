# Java Thread

### What is Thread?

​	In the definition of Baidu Encyclopedia,thread  is the smallest unit that the operating system can schedule operations. It is included in the process and is the actual unit of operation in the process.The reason why we need thread is that threads can be created and managed less operating system overhead and fewer system resources and a process need more system resources.

​	And how does a thread works?

​	When you new a thread, a thread was created by OS and then pushed into ready queue.The the state of this thread is in runnable. And When OS need it work, it start work and in a running state. And beteen it in runnable and running, there have some state a thread may go through.And when its task finally ended, it is in a dead state, and OS will release its resources.

​	The working machanism of thread may like this:

![1554507586590](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1554507586590.png)

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

