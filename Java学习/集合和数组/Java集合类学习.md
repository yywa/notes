# Java集合类学习

## 一、层次结构

- Collection

  - List
  - - LinkedList
  - - ArrayList
  - Set
  - - TreeSet
  - - HashSet

- Map

- - TreeMap

- - HashMap

- - - LinkedHashMap
##二、Collection

​	Collection是最基本的集合接口。JDK中不提供直接继承自Collection中的类，JDK提供的类都是继承自Collection的子接口。如List和Set。

​	因为Collection类继承了Iterable类.因此，不论Collection的实际类型如何，它都支持一个iterator()方法，可以通过迭代遍历Collection中的每一个元素。

```java
 Iterator it = collection.iterator(); // 获得一个迭代子xxxxxxxxxx
 while(it.hasNext()) {
 	Object obj = it.next(); // 得到下一个元素
 }
```



## 三、Set

​	用于存储无序元素，值不能重复。该集合中没有特有的方法，直接继承Collection类

##### 1.HashSet

​	HashSet不存入重复元素的规则，通过HashCode和equal判断

1. HashCode()方法返回该对象的哈希码值。返回的类型是int类型。因此，可能会返回一样的哈希值。也就是说，hashCode()相等的两个对象他们的equal()不一定相等，也就是说，hashCode()不是绝对可靠的。
2. Equal()相等的两个对象他们的hashCode()肯定相等，也就是用equal()对比是绝对可靠的。

   ​因此，需要大量而且快速的对比的话，可以先用hashCode()方法比对，然后再用equal()方法比对。这样效率比较高。

   ​当你试图把对象加入HashSet时，HashSet会使用对象的hashCode来判断对象加入的位置。同时也会与其他已经加入的对象的hashCode进行比较，如果没有相等的hashCode，HashSet就会假设对象没有重复出现。

   ​简单一句话，如果对象的hashCode值是不同的，那么HashSet会认为对象是不可能相等的。

   ​因此我们自定义类的时候需要重写hashCode，来确保对象具有相同的hashCode值。

   ​如果元素(对象)的hashCode值相同,是不是就无法存入HashSet中了? 当然不是,会继续使用equals 进行比较.如果 equals为true 那么HashSet认为新加入的对象重复了,所以加入失败。如果equals 为false那么HashSet 认为新加入的对象没有重复.新元素可以存入.

##### 总结：

​	元素的哈希值是通过元素的hashcode方法 来获取的, HashSet首先判断两个元素的哈希值，如果哈希值一样，接着会比较equals方法 如果 equls结果为true ，HashSet就视为同一个元素。如果equals 为false就不是同一个元素。

​	HashSet可以去除重复数据，但是是无序的。

##### 2.TreeSet

```java
public class Demo {
    public static void main(String[] args){
        TreeSet ts = new TreeSet();
        ts.add("ccc");
        ts.add("bbb");
        ts.add("ddd");
        ts.add("aaa");
        System.out.println(ts);
    }
}
```



输出的结果是  ![8D@6K5RQA{2ZRKB9MMBXS7](8D@6K5RQA{2ZRK]B9MMBXS7.png)

​	既然TreeSet可以自然排序，那么TreeSet必定是有排序规则的。

​	TreeSet存入字符串，字符串默认输出是按升序排列的。因为String类实现了一个接口Comparable。String类重写了该接口的compareto()方法

```java
public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;

        int k = 0;
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {
                return c1 - c2;
            }
            k++;
        }
        return len1 - len2;
    }

```

### 四、List

​	List实现Collection接口，它的数据结构是有序可以重复的结合。有三个实现类：ArrayList，LinkedList，Vector三个实现类。

##### 	1.ArrayList

  ![20161011235305081](20161011235305081.png)

​	查询速度快，增删改慢。

​	线程异步，不安全。如果想要实现ArrayList线程同步，应该使用Collections.synchronizedList将该列表包装起来。

```java
  List list = Collections.synchronizedList(new ArrayList(...));
```



##### 	2.LinkedList

 ![20161011235229018](20161011235229018.png)

​	链表结构，增删改速度快，查询慢。

​	LinkedList的本质是双向链表

1. LinkedList继承与AbstractSequentialList，并且实现了Dequeue接口
2. LinkedList包含两个重要的成员：header和size

   - header是双向链表的表头，它是双向链表节点所对应的类Entry的实例。Entry中包含成员变量：previous，next，element。其中，previous是该节点的上一个节点，next是该节点的下一个节点，element是该节点所包含的值。
   - size是 双向链表中的节点的个数。

​	**既然LinkedList实现了List接口，那么它是如何将双向链表和索引值联系起来的？**

​	它通过一个计数索引值来实现的。当我们调用get(int index)时，首先会比较location和双向链表长度的1/2；若后者大，则从链表头开始往后找，否者，则从链表尾开始向前找。

### 五、Map

​	key-value形式存值。

##### 	1.HashMap

​	**特点：**保存元素时，先进后出，无序性，查询效率高，key-value可以为空，但最多只能为一个NULL(数据不能重复)；不支持线程同步，如有需要，可以使用Collection的synchronizedMap方法。

##### 	2.TreeMap

​	特点：有序，通过key值的顺序，进行排序。