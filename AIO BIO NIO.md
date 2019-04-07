# Java BIO NIO  AIO

### Traditional BIO Mode

​	BIO use a traditional mode to connect to each client! The server create a thread for each client to handle their own socket. And I have already give some examples in the other artical.You can See more about it by [cliking it]().

​	The Client num is equal to the thread num.It will cause many problem.As well all know, a thread need some resource,when the number of thread increase,the internal memory will decrease a lot. We assume that a thread cost 1M internal memory, and we have 10000 client to access,this program will cost 10G internal memory, it's a terrible thing.

​	The BIO mode :	![01](http://blog.anxpp.com/usr/uploads/2016/05/549520916.png)

​	You can use a ThreadPool to manage this thread,we can call"Pseudo asynchronous I/O Mode",because it use synchronous blocking I/O as its base.

Like this:

![02](http://blog.anxpp.com/usr/uploads/2016/05/614169023.png)



------------------------------------------------

### NIO Mode

​	NIO use cannels to contack with Client.And a Selector to monitor these cannels,a single thread to manage the selector.The graph below show the working mechanism:



![img](http://ifeve.com/wp-content/uploads/2013/06/overview-selectors.png)

​	And for each cannel,operating system give a buffer to put some information.And there have two kinds of channel:

​	1.SelectableChannel---subclass:  ServerSocket and ServerSocketChannel

​	2.FileChannel

And how we use it?

​	You will create a Class named ServerHandle which implements Runnable.

​	The main idea about this is just to create a super class(here means this class is powerful,can do most of the thing,such as manage channels by using selector, registe selector and other methods),the structure of this class may like this:

```java
public class ServerHandle implenments Runnable{
    //some varible here
    //....
    ServerHandle(int port){
        //new Selector here
        //new ServerSocketChannel here
        //Regist selector here
        //initail serversocketchannel,such as configureBlocking,bind,etc
    }
    
    @overright
    public void run()
    {
        while(!stop)
        {
            //manage channels
        }
    }
    
    public void stop()
    {
        //make the stop flag true
    }
} 
```



​	First,new a Selector and a serversocketcannel;

```java
private Selector selector = Selector.open();
private ServerSocketChannel serversocketchannel = ServerSocketChannel.open();
```

​	And then set some parameters in its contructor like

```java
serversocketchannel.configureBolcking(false);
//The channel are in a non-blocking mode!
serversocketchannel.socket().bind(new InetSocketAddress(port),1024);
//bind the channel with port 8198
serversocketchannel.register(selector,SelectionKey.OP_ACCEPT);
//register selector in the serversocketchannel
```

​	After initiation, overwrite the run()method to make it run successfully.

```java
@Override
public void run()
{
    selector.select(1000);//wake up selector every 1 second.
    Set<SelectionKey> keys = selector.selectedKeys();
    //Get the selected keys,each key have some information about a channel.
    for(Iterator<SelectionKey> it;it.hasNext();)
    {
        SelectionKey key = it.next();
        try{
            ServerSocketChannel ssc = (ServerSocketChannel)key.channel();
            SocketChannel sc = ssc.accept();
            sc.configureBlocking(false);
            //Just like the BIO style below:
            //ServerSocket ss = new ServerSocket();
            //Socket s = ss.accept();
            //......
            sc.register(selector,SelectionKey.OP_READ);
            //register for read_only
            if(key.isReadable())
            {
                ByteBuffer buffer = ByteBuffer.allocate(1024);
                //new a ByteBuffer for the channle
                sc.read(buffer);
                //Reads a sequence of bytes from 
                //this channel into the given buffers.
                //now you can display the buffer use the stream you want.
            }
            
        }catch(Exception e)
        {
            e.printStackTrace();
        }
    }
}
```

Don't know it Clearly? May be the Graph below will help you a lot:

![1554037270745](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1554037270745.png)



​	And then ,we can use this Class (ServerHandle) , and new a instance of this class to manage all this channels.Like this:

```java
public class ServerTest{
    private static int port = 8198;
    private static ServerHandle serverhandle;
    public static void main(String[] args)
    {
        serverhandle = new ServerHandle(port);
        new Thread(serverhandle).start();
        
    }
}
```

And NIO client part are similar to BIO.



---

### AIO MODE

​	We do the same way that NIO MODE do,first ,we create a class named AsyncServerHandler,the structure of this class may like this:

```java
public class AsyncServerHandler{
    private static AsynchronousServerSocketChannel channel;
    private static CountDownLatch latch;
    public AsynchronousServerSocketChannel(int port)
    {
        try{
        channel = AsynchronousServerSocketChannel.open();
        channel.bind(new InetSocketAddress(port));
            
        }catch(Exception e)
        {
            //...
        }
    }
    @override
    public void run()
    {
        try{
            latch = new CountDownLatch(1);
            channel.accept(this,new AcceptHandler());
            latch.await();
        }catch(Exception e)
        {
            e.printStackTrace();
        }
    }
}
```



​	Now we explain the class AcceptHandler.The Structure of this class is like this:

```java
class AcceptHandler implements CompletionHandler<AsynchronousSocketChannel, AsyncServerHandler> {
    @override
    public void completed(AsynchronousSocketChannel channel,
                          AsyncServerHandler serverHandler) {
        //Do it when it accept succefully.
        //accept the request from request
        //some operation here.
    }
    
    @override
    public void failed(Throwable exc, AsyncServerHandler serverHandler) {
        
    }
}
```

​	And how does it work?

​	It work as a handler for consuming the result of an asynchronous I/O operation. (Defined in Java api doc). the two override method,complted() for  an operation has completed and failed() for an operation has failed!

​	The working mode of AIO is like this :

![1554299347126](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1554299347126.png)

​	AIO MODE is similar to NIO MODE, and it use a handler instance which implement interface ComletionHandler<K,V> .But my understanding of this mode is uncomplete. And I don't know  underlying mechanism  of this mode for detail.So I will rewrite this mode in a new article.

​	[New artical ]()





### 											Thanks for reading.

### 						    Contack with me if there have problem:

​																		emial:271669603@qq.com

​														

------

**Reference resources:**

[Java 网络IO编程总结（BIO、NIO、AIO均含完整实例代码）](<http://blog.csdn.net/anxpp/article/details/51512200>)

