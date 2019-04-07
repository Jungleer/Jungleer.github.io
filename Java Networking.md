# Java Networking

### How  to connect to a server by Java?

​	Let connect to server by telnet first.What's telnet?You can see [here](http://www.runoob.com/linux/linux-command-manual.html) to know more about it!

​	Now,let's assume you have know telnet well down.Let's telnet a server: **time-A.timefreq.bldrdoc.gov**   port:**13**  .In Windows, use command :

telnet time-A.timefreq.bldrdoc.gov 13 ,the result is like this:

![1553867605286](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1553867605286.png)

This server return a time to you.

Let's connect in  by Java Language.

```java
public static void main(String[] args)throws IOException
{
	try(Socket s = new Socket("time-A.timefreq.bldrdoc.gov",13))
    {
    	InputStream inStream = s.getInputStream();
    	Scanner in = new Scanner(inStream);
    	while(in.hasNextLine())
        {
        	String line = in.nextLine();
        	System.out.println(line);
        }
    }
}
```

The Principle of it is easy.

![image](https://images.cnblogs.com/cnblogs_com/skynet/201012/201012122157494693.png)

​	Use  Three-handshake protocol ,client get a socket successfully which connect to the server.And then,you can use .getInputStream() to get an inputstream to write information to your command.

​	Also,you can .getOutputStream to write some command to control the remote server.

​	And you can set socket timeouts by using s.setSoTimeout(int millionseconds) ,it will throw out a SocketTimeoutException,a subclass of InterruptedIOException.



​	For most of the time, you want to get ip address of some site, or get site name by ip address.You will use class InetAddress to get all ip addresses of one site name.

​	Just like you want to know all ip address of www.baidu.com ,you can use this code:

```java
        if(args.length>0)
        {
            String host=args[0];
            InetAddress[] addresses = InetAddress.getAllByName(host);
            for(InetAddress a:addresses)
            {
                System.out.println(a);
            }

        }
        else{
            InetAddress localhostAddress = InetAddress.getLocalHost();
            System.out.println(localhostAddress);
        }
```

For one InetAddress a,you can use .getAddress to reutnr an array of bytes like 132.163.4.104.But there is some problem if you print it out.You need to mod256 for every bytes of the byteArray.

![1553913387891](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1553913387891.png)

The table below is some method of InetAddress:

| Return Type   | Function Name  | Parameters  | Description                                                  |
| ------------- | -------------- | ----------- | ------------------------------------------------------------ |
| InetAddress   | getByName      | String host | return the first one it get,a static method                  |
| InetAddress[] | getAllByName   | String host | return all,a static method                                   |
| byte[]        | getAddress     |             | return a bytes like{'192','168','123','124'},but if you wnat to use it,you must mod256 for each bytes in bytesArray |
| String        | getHostAddress |             | return a string like"192.15.56.56"                           |
| String        | getHostName    |             | return the host name,like localhost.                         |



------

### Let's chat with your local server---localhost

Using  a ServerSocket to create  a server.Use .accept to acquire a Socket object.

The Program is like this:

```java
public class EchoServer {
    public static void main(String args[]) throws IOException {
        try(ServerSocket s = new ServerSocket(8189))
        {
            try(Socket incoming =s.accept())
            {
                InputStream inputStream=incoming.getInputStream();
                OutputStream outputStream = incoming.getOutputStream();
                try(Scanner in = new Scanner(inputStream))
                {
                    PrintWriter out = new PrintWriter(outputStream,true);
                    out.println("Hello! Enter BYE to exit");

                    boolean done = false;

                    while(!done&&in.hasNextLine())
                    {
                        String line = in.nextLine();
                        out.println("Echo: "+line);
                        if(line.trim().equals("BYE")) done=true;
                    }
                }
            }
        }
    }
}
```

![1553944940420](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1553944940420.png)

Also,you can use multiply thread to accept the link bwteen server and client.

```java
public class ThreadedEchoServer {
    public static void main(String args[])
    {
        try{
            int i=1;
            ServerSocket serverSocket= new ServerSocket(8198);
            while(true)
            {
                Socket socket=serverSocket.accept();
                System.out.println("Spawning  "+i);
                Runnable runnable = new ThreadEchoHander(socket);
                Thread thread = new Thread(runnable);
                thread.start();
                i++;
            }
        }catch (Exception e)
        {
            e.printStackTrace();
        }
    }

}

class ThreadEchoHander implements Runnable{
    private Socket incoming;

    public ThreadEchoHander(Socket i )
    {
        this.incoming=i;
    }
    @Override
    public void run() {
        try{
            InputStream inputStream = incoming.getInputStream();
            OutputStream outputStream = incoming.getOutputStream();

            Scanner in =new Scanner(inputStream);
            PrintWriter writer = new PrintWriter(outputStream,true);

            writer.println("Welcome!Echo BYE to exit");

            boolean done=false;
            while(!done&&in.hasNextLine())
            {
                String line=in.nextLine();
                writer.println("What you say is"+line);
                if(line.trim().equals("BYE"))done=true;
            }

        }catch(Exception e){
            e.printStackTrace();
        }
        finally {
            try{incoming.close();}catch (Exception e){e.printStackTrace();}
        }
    }
}

```

The main idea of this code is :

1. First to create a ThreadEchoHandler class which have a private Socket.And the run() method just to process what one single Socket do.

2.  Second, in the main thread,when the socketserver accept,just create a incoming socket,and use this socket to initialize a new ThreadedEchoHandler Object,and run it.

   

   In many cases,when we close a Socket object   of  a client,we don't want to close the OutputStream of Server so that we can server other Client.

​	You can use Half-Close which provides the ability for one end of a socket connection to terminate its output while still receiving data from the other end.

Use shutdownOutput or shutdownInput to close the Stream you want to close of a Socket.

------

### Getting Web Data

​	As we search sth, we can get information by using browser via a URL.Java provide class URL to let you aceess a web server to get its web information, or we can say its source code!

​	To get web Data, you need new a URL object  like:

```java
URL url = new URL (UrlPath);
```

​	And use this url to connect to the host. And if you have this connection, you can use it to get inputstream or outputstream to do what you can do.Such as:

```java
URLConnection connection = url.openConnection();
Scanner in = new Scanner(connection.getInputStream());
PrintWriter out = new PrintWriter(new FileWriter("your local path here"),true);
while(in.hasNextLine())
{
    String line = in.nextLine();
    out.println(line);
}
```

​	Now, you can process data by XML.To get your needed information.

​	Also , if you want to know how to Paser xml files, you can [Click here]()

​	And most of the time , we want to know some information about the HTTP respone header. And What's header? You can see them in your browser like this:

![1554273704534](C:\Users\李俊\AppData\Roaming\Typora\typora-user-images\1554273704534.png)

And now , can get some information about it by some method java has provided.

​	And If you do not need InputStream , you can use setDoInput(false) to achieve your goal.

---

### 											   Thanks for reading.

### 						    Contack with me if there have problem:

​																		emial:271669603@qq.com

