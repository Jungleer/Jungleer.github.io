# Java Basic DataStruct

​	In this chapter, I will introduce some frequently-used datastruct in Java. The top interface or class of them are called Collection, Map and Iterator. The Link between them are like this:

 ![1555069261919](..\img_lib\1555069261919.png)

​	And Let's know the most important one------Collection first.

​	Collection is defined as a group of objects, often of the same sort, that have been collected  in the Oxford Advanced Learner's English Chinese Dictionary. It don't pay attention to how them group, what's the structure of them. It just a interface to defind some usal method of a collection.

​	The most important part of Collection is its subinterface. It has there important subinterface, Queue, List and Set.

​	And Use these subinterface, it can create any kinds of class in every possible combinations.

​	Let introduce the List first!

​	List is an ordered collection (also known as a *sequence*).  The user of this interface has precise control over where in the list each element is inserted.  The user can access elements by their integer index (position in the list), and search for elements in the list.

​	And some frequent-used method of this interface.

| Return Type and Function Name               | Description |      |
| ------------------------------------------- | ----------- | ---- |
| boolean add(E e)                            |             |      |
| boolean contain(Object o)                   |             |      |
| int IndexOf(Object o)                       |             |      |
| boolean remove(int index)                   |             |      |
| List<E> subList(int fromIndex, int toIndex) |             |      |

​	And all this method is easy to use. Now we introduce an important method,sort() method of it.

​	The sort method is defind like this:

```java
    default void sort(Comparator<? super E> c) {
        Object[] a = this.toArray();
        Arrays.sort(a, (Comparator) c);
        ListIterator<E> i = this.listIterator();
        for (Object e : a) {
            i.next();
            i.set((E) e);
        }
    }
```

​	And if you want to use this method, you should Rewrite a class implements Comparator<? super E>.

```java
class MyComparator implements Comparator<point>
{

    @Override
    public int compare(point o1, point o2) {
        if(o1.x>=o2.x&&o1.y>=o2.y)
            return 1;
        else if(o1.x<=o2.x&&o1.y<=o2.y)
            return -1;
        else return 0;
    }
}
```

​	And if o1 equal(the meaning of equal here is defind by yourself) o2, return 0; o1 <o2 return -1,o1 > 02 return 1; So, we will make it in an ascending order.

​	The source code:

```java
public class TryList {
    public static void main(String[] args) {
        List<point> list = new Vector<point>();
        point p = new point(9000,1000);
        point p1 = new point(2,7);
        point p2 = new point(3,4);
        point p3 = new point(5,6);
        point p4 = new point(11,1);
        point p5 = new point(2,3);
        point p6 = new point(5,1);
        point p7 = new point(11,3);
        point p8 = new point(7,9);
        point p9 = new point(8,2);
        list.add(p);list.add(p1);list.add(p2);
        list.add(p3);list.add(p4);list.add(p5);
        list.add(p6);list.add(p7);list.add(p8);
        list.add(p9);
        list.sort(new MyComparator());
        //Collections.sort(list,new MyComparator());
        for(int i=0;i<list.size();i++)
            System.out.println(list.get(i).x+" "+list.get(i).y);
    }
}
```

​	The screenshoot of the result:

![1555125884614](..\img_lib\1555125884614.png)

​	And also, you can use Collections.sort(List<T> list, Comparator<? super T> c)method to sort it.

​	And the basic method of list and its subclass is easy, we just talk about the difference characteristic among them.

| Name      | difference characteristic                                    |
| --------- | ------------------------------------------------------------ |
| ArrayList | ArrayList is the most commonly used List implementation class, which is implemented internally through arrays, allowing quick random access to elements.It's easy to visit element, but hard to remove or add element (O(n)). |
| Vector    | The same as ArrayList ,but it supports thread synchronization |
| LinkList  | LinkedList uses linked list structure to store data. It is very suitable for dynamic insertion and deletion of data. Random access and traversal speed is relatively slow |



---

### Set

​	Set focuses on unique properties. The system set is used to store disordered elements (the order of storage and retrieval is not necessarily the same), and the values cannot be repeated. The essence of object equality is to judge the hashCode value of the object (java is the serial number calculated by the memory address of the object). If you want to make two different objects equal, you must override the hashCode method and equals method of Object.

​	 The elements in the Set are not duplicated, and they are stored through hash values. 

​	The structure of Set is like this:

![1555156964230](..\img_lib\1555156964230.png)

​	The two element ,even then do not equal, but they may have the same hashcode. So if you use equals to know whether the two number is real equal.

​	Some subClass of Set and their realization.