# 0831 Set与泛型&amp;Map学习笔记

# set
## HashSet
#### HashSet如何确保无重复
```java
//Set 数据无序，对象不可以重复
//HashSet 无序，不可以重复。hash代表使用的hash表实现数据的存储。
//根据要添加的数据的hashcode（）计算出的值进行某种运算，使用这个运算结果计算尺一个数组的下标值
//这个下标就是数据的存放位置。
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        //此类实现Set接口，由哈希表（实际为HashMap实例）支持
        HashSet<String > set = new HashSet<>();
        // 使用add方法添加时已经保证不重复
        set.add("hello");
        set.add("hehe");
        set.add("html");
        // 使用add添加时就会判断是否重复
        //如何判断是否重复？依据hashcode以及equals双重验证
        //先依据hashcode方法，如果哈希值相同，再调用equals方法比较。如果返回true则认为相同对象，不加入集合。
        //否则链接在后面
        set.add("hehe");
        // 无序且不可重复从这里可以体现
        System.out.println(set);// [hehe, html, hello]
        
        //构造一个包含指定集合中的元素的新集合。 
        // HashMap使用默认负载因子（0.75）创建，初始容量足以包含指定集合中的元素。
        HashSet set1=new HashSet(Arrays.asList(11, 2, 3, 4, 5, 5, 6));
        System.out.println(set1);//[2, 3, 4, 5, 6, 11]
    }
}
```
#### 理解set的无重复
```java
import java.util.*;

public class Demo9 {
	public static void main(String[] args) {
		HashSet set = new HashSet();

		set.add(new Person());
		set.add(new Person());
		set.add(new Person());
		//最终判别是否相同用equals方法
		//虽然hash一样，但是new的三个对象用equals比较出来不一样，所以运行结果是3.
		System.out.println(set.size());//3
	}
}

class Person{
	public int hashCode() {
		return 66;
	}
}

```


#### 利用HashSet去除重复
```java
//去除重复的王五，姓名 年龄一致认为是同一个
//利用set去重
//哈希值相同的未必是同一对象。不同的对象哈希值肯定不同
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        HashSet<Student> set = new HashSet<>();
        set.add(new Student("bob", 23));
        set.add(new Student("lisi", 21));
        set.add(new Student("wangwu", 20));
        set.add(new Student("zhaoliu", 23));
        set.add(new Student("wangwu", 20));
        System.out.println(set);// [bob, zhaoliu, lisi, wangwu]
    }
}
class Student {
    private String name;
    private int age;
    Student() {
    }
    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    // 重写toString方便输出对象
    public String toString() {
        return name;
    }
    // 重写hashCode方法
    public int hashCode() {
        return name.hashCode() + age * 36;
    }
    // 重写equals方法.不然调用内置的会比较内存地址。而两个王五都是new出来的，内存地址必然不同
    // 所以内置的无法比较两个new出来的对象是否相同
    public boolean equals(Object obj) {
        if (!(obj instanceof Student)) {
            throw new RuntimeException();
        }
        Student stu = (Student) obj;
        return this.name.equals(stu.name) && this.age == stu.age;
    }
}
```
## TreeSet
**TreeSet可以对Set集合中的元素进行排序。**
底层数据结构：二叉树。
保证元素唯一：comoareTo  return 0；
**TreeSet排序的第一种方式：让元素自身具备比较性。**元素实现Comparable接口覆盖compareTo方法，这种方式也称为元素的自然顺序，或者叫默认顺序。
 
TreeSet排序的第二种方式：当元素自身不具备比较性时，或者具备的比较性不是所需要的，这时需要让集合自身具备比较性。在集合初始化时，就有了比较方式。


2种实现排序方式视情况而用。
**(1)自然顺序(Comparable)**
TreeSet类的**add()方法中会把存入的对象提升为Comparable类型**
调用对象的compareTo()方法和集合中的对象比较.
根据compareTo()方法返回的结果进行存储
(2)比较器顺序(Comparator)
创建TreeSet的时候可以指定一个Comparator
如果传入了Comparator的子类对象，那么TreeSet就会按照比较器中的顺序排序
调用的对象是compare方法的第一个参数，集合中的对象是compare方法的第二个参数
(3)两种方式的区别
TreeSet构造函数什么都不传，默认按照类中Comparable的顺序(没有就报错ClassCastException)
TreeSet如果传入Comparator，就优先按照Comparator
如果不想保证元素的唯一性，改一下compare方法就可以了，永远不要让它返回0。
 
#### TreeSet入门
```java
import java.util.TreeSet;
public class Demo1 {
	public static void main(String[] args) {
		TreeSet ts = new TreeSet();
		ts.add('c');// 在是同add方法时就排序了
		ts.add('b');// "b".compareTo("c")
		ts.add('a');
		// 可以对添加的对象进行排序。hashset不是如此
		// 向集合中添加的对象所属的类必须实现Comparable接口中的compareTo方法
		// Treeset集合就是调用所添加对象的CompareTo方法确定谁大谁小的方法
		System.out.println(ts);// [a, b, c]
			
	}
}


```
#### 理解TreeSet 自然排序
```java
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        //TreeSet是基于TreeMap实现的，支持自然排序和自定义排序，可以进行逆序输出；
        //TreeSet不允许null值
        TreeSet<Person> ts = new TreeSet<>();
        // 当compareTo返回值为0时，认为是同一个对象
        ts.add(new Person(18));
        ts.add(new Person(28));
        ts.add(new Person(8));
        ts.add(new Person(25));
        ts.add(new Person(20));
        ts.add(new Person(5));
        ts.add(new Person(9));
        ts.add(new Person(30));
        System.out.println(ts);//[5, 8, 9, 18, 20, 25, 28, 30]
    }
}
//该接口对实现它的每个类的对象强加一个整体排序。
//这个排序被称为类的自然排序 ，类的compareTo方法被称为其自然比较方法
class Person implements Comparable {
    private int age;
    Person() {
    }
    Person(int age) {
        this.age = age;
    }
    // compareTo c不用大写
    public int compareTo(Object obj) {
        if (!(obj instanceof Person)) {
            throw new ClassCastException("类型不对");
        }
        Person ren = (Person) obj;
        if (this.age > ren.age)
            return 5;
        else if (this.age < ren.age)
            return -5;
        else
            return 0;
    }
    public String toString() {
        return "" + age;
    }
}
```
#### 更改TreeSet默认的排序机制
```java
//理解二叉树的用途
import java.util.*;
public class Demo {
	public static void main(String[] args) {
		TreeSet ts = new TreeSet();
		// TreeSet只看compareTo的返回值来决定输出顺序
		ts.add(new Person(18));
		ts.add(new Person(28));
		ts.add(new Person(8));
		ts.add(new Person(25));
		ts.add(new Person(20));
		// 因为compareTo固定返回-1，二叉树固定朝左走。
		System.out.println(ts);// [20, 25, 8, 28, 18]
	}
}

class Person implements Comparable {
	private int age;

	Person() {
	}

	Person(int age) {
		this.age = age;
	}

	// compareTo c不用大写
	public int compareTo(Object obj) {
		if (!(obj instanceof Person)) {
			throw new ClassCastException("类型不对");
		}
		Person ren = (Person) obj;
		return -1;
	}

	public String toString() {
		return "" + age;
	}
}
```


![image.png](https://cdn.nlark.com/yuque/0/2020/png/2145106/1598842922564-b4719ef8-5e25-4c33-a464-38efbedb14bf.png#align=left&display=inline&height=311&margin=%5Bobject%20Object%5D&name=image.png&originHeight=622&originWidth=1426&size=42265&status=done&style=none&width=713)


#### 理解TreeSet不重复的原理
```java
//理解Set的不重复
import java.util.*;
public class Demo {
	public static void main(String[] args) {
		TreeSet ts = new TreeSet();
		ts.add(new Person(18));
		ts.add(new Person(28));
		ts.add(new Person(8));
		ts.add(new Person(25));
		ts.add(new Person(20));
		// 由于Set不能重复，所以return 0只能输出一个数据，重复的数据不予输入。
		System.out.println(ts);// [18]
	}
}

class Person implements Comparable {
	private int age;

	Person() {
	}

	Person(int age) {
		this.age = age;
	}

	// compareTo c不用大写
	public int compareTo(Object obj) {
		if (!(obj instanceof Person)) {
			throw new ClassCastException("类型不对");
		}
		Person ren = (Person) obj;
		return 0;
	}

	public String toString() {
		return "" + age;
	}
}
```
#### 自行实现comparable接口


```java
//理解comparable接口
import java.util.*;
public class Demo {
	public static void main(String[] args) {
		TreeSet ts = new TreeSet();
		ts.add(new Person(18,"lisi"));
		ts.add(new Person(28,"wangwu"));
		ts.add(new Person(8,"xiaobai"));
		ts.add(new Person(25,"zhaosi"));
		ts.add(new Person(20,"liuneng"))
		System.out.println(ts);
	}
}

//对象所属的类实现comparable接口
//Java不支持预算符重载，我们通过实现Comparable接口达到相同的目的。当类实现了Comparable接口，则认为这个类的对象之间是可比较的。、、
//比较此对象与指定对象的顺序。如果该对象小于、等于或大于指定对象，则分别返回负整数、零或正整数。
//ClassCastException - 如果指定对象的类型不允许它与此对象进行比较。
/*  Comparable相当于“内部比较器”，而Comparator相当于“外部比较器”。
 <1> 若一个类实现了Comparable接口直接就成为一个可以比较的对象，但是需要修改源代码。
 <2> Comparator是比较器，若需要控制某个类的次序，则需要定义一个外部类实现Comparator接口， 当某个自定义的对象需要作比较的时候，把比较器和对象一起传递过去就可以比大小了。不需要修改源代码。
 */

/*
 * Comparator位于包java.util下，而Comparable位于包java.lang下，
 * Comparable接口将比较代码嵌入自身类中，而后者在一个独立的类中实现比较。 
 * 如果类的设计师没有考虑到Compare的问题而没有实现Comparable接口，可以通过 Comparator来实现比较算法进行排序，并且为了使用不同的排序标准做准备，

 */
class Person implements Comparable {
	private int age;
	private String name;

	Person() {
	}

	Person(int age,String name) {
		this.age = age;
		this.name=name;
	}

	// compareTo c不用大写
	public int compareTo(Object obj) {
		if (!(obj instanceof Person)) {
			throw new ClassCastException("类型不对");
		}
		Person ren = (Person) obj;
		return ren.age-this.age;
	}

	public String toString() {
		return name+"" + age;
	}
}
```
#### 当你想自定义排序
```java
//追加另一种排序方式
//类现有的比较大小的方式不是我们需要的，现有的比较大小的方式还不能改动
//解决犯法：自定义一种比较大小的方式，实现Comparator接口
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        // 优先使用Comparator，此时Comparable失效
        // 这个次序从源代码可以看出来
        //TreeSet的构造函数可以是带参的。
        CombyName cb = new CombyName();
        //构造一个新的，空的树集，根据指定的比较器进行排序。
        //插入到集合中的所有元素必须由指定的比较器相互比较 ： comparator.compare(e1, e2)。
        //如果用户尝试向该集合添加一个违反此约束的元素，则add调用将抛出ClassCastException 。
        TreeSet<Person> ts = new TreeSet<>(cb);
        ts.add(new Person(18, "lisi"));
        ts.add(new Person(28, "wangwu"));
        ts.add(new Person(8, "xiaobai"));
        ts.add(new Person(25, "zhaosi"));
        ts.add(new Person(20, "liuneng"));
        System.out.println(ts);//[lisi18, liuneng20, wangwu28, xiaobai8, zhaosi25]
    }
}
//自定义一种比较大小的方式：单独写一个类，类中实现比较大小
//我们设计类的时候，可能没有考虑到让类实现Comparable接口，那么就需要用到另外的一个比较器接口Comparator
class CombyName implements Comparator<Person> {
    public int compare(Person obj1, Person obj2) {
        if (obj1 == null)
            throw new ClassCastException("类型不对");
        if ((obj2 == null))
            throw new ClassCastException("类型不对");

        return obj1.getName().compareTo(obj2.getName());
    }
}
class Person implements Comparable{
    private int age;
    private String name;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    Person() {
    }
    Person(int age, String name) {
        this.age = age;
        this.name = name;
    }
    // compareTo中 c不用大写
    public int compareTo(Object obj) {
        if (!(obj instanceof Person)) {
            throw new ClassCastException("类型不对");
        }
        Person ren = (Person) obj;
        return ren.age - this.age;
    }
    public String toString() {
        return name + "" + age;
    }
}
```
#### 利用TreeSet进行排序
```java
//对字符串中的数字进行排序
import java.util.TreeSet;
public class Demo {
    public static void main(String[] args) {
        String str = "23 45 12 6 78 3";
        String[] arr = str.split(" ");
        TreeSet<Integer> ts = new TreeSet<>();
        for (String s : arr) {
            // Integer已经实现了Comparable接口，本身就是按照数字大小排序的。所以
            // 无需我们手动定义、
            ts.add(Integer.parseInt(s));
        }
        System.out.println(ts);// [3, 6, 12, 23, 45, 78]
    }
}
```
#### 
# 泛型
#### 为什么需要泛型
```java
//泛型的需求
//java.lang.ClassCastException
import java.util.*;

public class Demo3 {
	public static void main(String[] args) {
		ArrayList list = new ArrayList();

		list.add("hello");
		list.add("bigdata");
		list.add("java");
		list.add(66);// java.lang.ClassCastException

		Iterator ite = list.iterator();
		while (ite.hasNext()) {
			Object obj = ite.next();
			String str = (String) obj;
			// 编译时无问题，但是运行时出错，因为66 整数无法转换成大写。
			System.out.println(str.toUpperCase());
		}
	}
}
```
#### 泛型提高程序安全性
```java
//泛型。通过<>接收一种引用数据类型，作用是在编译时期就检查集合中存放的数据和泛型指定的数据类型是否一致。
//如果不一致，编译出错。从而把运行时起的问题转移到编译时期。提高了程序的安全性
//jdk1.5后出现。并非天生就有的。
/*，在编译之后程序会采取去泛型化的措施。也就是说Java中的泛型，只在编译阶段有效。
 * 在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦出，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。
 * 也就是说，泛型信息不会进入到运行时阶段。
 * */
import java.util.*;

public class Demo3 {
	public static void main(String[] args) {
		// 泛型类ArrayList
		ArrayList<String> list = new ArrayList<>();

		list.add("hello");
		list.add("bigdata");
		list.add("java");
		// 泛型类Iterator
		Iterator<String> ite = list.iterator();
		while (ite.hasNext()) {
			String obj = ite.next();
			System.out.println(obj.toUpperCase());
		}
	}
}

```
#### 泛型缺陷的例子
```java
/在定义类时定义了泛型，这个类才能使用泛型
//使用泛型前
class Student{	
}

class Worker{	
}


class Tool{
	private Object obj;
	
	public void setObj(Object obj) {
		this.obj=obj;
	}
	public Object getObj() {
		return obj;
	}
}


public class Demo4 {
	public static void main(String[] args) {
		Tool tool=new Tool();
		tool.setObj(new Student());
		// java.lang.ClassCastException: class com.qianfeng.Day21.Student cannot be cast to class com.qianfeng.Day21.Worker
		//类型转换异常
		//不用泛型存在的安全隐患
		Worker worker=(Worker)tool.getObj();
	}
}


```
#### 学习如何定义泛型类
```java
//在定义类时定义了泛型，这个类才能使用泛型
class Student {
}

class Worker {
}

//使用泛型定义泛型类
//就是把数据类型都用大写E替代
class Tool<E> {
	private E obj;

	public void setObj(E obj) {
		this.obj = obj;
	}

	public E getObj() {
		return obj;
	}
}

public class Demo4 {
	public static void main(String[] args) {
		Tool<Student> tool = new Tool<>();
		tool.setObj(new Student());
		tool.getObj();
	}
}

```


#### 泛型方法的3种情况            
```java
//泛型方法
public class Demo5 {
	public static void main(String[] args) {
		// 类上的泛型是String类型，所以show方法参数就是String方法
		Test<String> test = new Test<>();
		test.show("77");// 7

		// 非静态方法
		test.fun("hehe");// hehe
		test.fun(3);// 3

		// 静态方法调用
		Test.ff("hello");// hello
		Test.ff(88);// 88

	}

}

class Test<T> {

	// 当类上的泛型确定了。该方法的参数类型才能确定
	public void show(T t) {
		System.out.println(t);
	}

	// 方法自己使用泛型，和类上的泛型没有关系
	// 与类无关的泛型
	public <E> void fun(E e) {// 参数的类型什么类型都可以
		System.out.println(e);
	}

	// 静态方法只能自己用泛型，泛型是在创建对象时确定的，而静态方法不需要对象、
	// 就是说不能和类上的泛型保持一致
	public static <E> void ff(E t) {
		System.out.println(t);
	}
}
```
#### 泛型接口
**实现接口时明确泛型**
```java
//泛型接口

public class Demo6 {
	public static void main(String[] args) {
		Test1 t = new Test1();
		t.show(9);// 9
		t.fun("8");// 9
	}
}

class Test1 implements inter<Integer> {
	public void show(Integer e) {
		System.out.println(e);
	}

	public void fun(String t) {
		System.out.println(t);
	}
}

//接口的泛型写在接口名后面
interface inter<E> {
	// 和接口上的数据类型保持一致。
	void shwo(E e);

	void fun(String t);
}

```
**实现接口时不明确泛型**
```java
//泛型接口
public class Demo6 {
	public static void main(String[] args) {
		Test2<String> t = new Test2<>();
		t.fun("8");// 9
	}
}

//实现的时候也可以先不明确泛型。待调用时再明确类型
class Test2<E> implements inter<E>{
	public void show(E e) {
		System.out.println(e);
	}
	public void fun(String t) {
		System.out.println(t);
	}
}

//接口的泛型写在接口名后面
interface inter<E> {
	// 和接口上的数据类型保持一致。
	void show(E e);

	void fun(String t);
}

```


#### 使用泛型迭代
```java
//使用泛型迭代
import java.util.*;
public class Demo7 {
	public static void main(String[] args) {
		ArrayList<String> list1 = new ArrayList<>();

		list1.add("java1");
		list1.add("java1");
		list1.add("java1");

		ArrayList<Integer> list2 = new ArrayList<>();
		list2.add(2);
		list2.add(3);
		list2.add(4);

		diedai(list1);
		diedai(list2);

	}

	// 使用泛型迭代
	public static <E> void diedai(Collection<E> col) {
		Iterator<E> ite = col.iterator();
		while (ite.hasNext()) {
			E ele = ite.next();
			System.out.println(ele);
		}
	}
}

```


#### 通配符
```java
//泛型中使用通配符
import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

public class Demo8 {
	public static void main(String[] args) {
		ArrayList<String> list1 = new ArrayList<>();

		list1.add("java1");
		list1.add("java1");
		list1.add("java1");

		ArrayList<Integer> list2 = new ArrayList<>();
		list2.add(2);
		list2.add(3);
		list2.add(4);

		diedai(list1);
		diedai(list2);
	}

	// 通配符写法
	
	public static void diedai(Collection<?> col) {
		Iterator<?> ite = col.iterator();
		while (ite.hasNext()) {
			Object ele = ite.next();
			System.out.println(ele);
		}
	}
}

```


#### 泛型限定
```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;

//泛型限定。？ extends E:限定了上限，只能是E或者E的子类
//? super E 只能是E或E的父类类型，限定下限
class Person {
	String name;

	public Person(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}

	int age;

	Person() {

	}

	public String toString() {
		return name + age;
	}

}

class Student extends Person {
	Student() {
	}

	public Student(String name, int age) {
		super(name, age);
	}

	public String toString() {
		return name + age;
	}
}

class Worker extends Person {
	Worker() {
	}

	Worker(String name, int age) {
		super(name, age);
	}

	public String toString() {
		return name + age;
	}

}

//使用泛型定义泛型类
//就是把数据类型都用大写E替代
class Tool<E> {
	private E obj;

	public void setObj(E obj) {
		this.obj = obj;
	}

	public E getObj() {
		return obj;
	}
}

public class Demo9 {
	public static void main(String[] args) {
		ArrayList<Student> list1 = new ArrayList<>();

		list1.add(new Student("xiaoming", 20));
		list1.add(new Student("xiaohong", 22));
		list1.add(new Student("libai", 21));

		ArrayList<Worker> list2 = new ArrayList<>();
		list2.add(new Worker("like", 89));
		list2.add(new Worker("ops", 29));
		list2.add(new Worker("tt", 67));

		diedai(list1);
		diedai(list2);

	}

	public static void diedai(Collection<? extends Person> col) {
		Iterator<? extends Person> ite = col.iterator();
		while (ite.hasNext()) {
			Person ele = ite.next();
			System.out.println(ele);
		}
	}

}
```
# Map
**HashMap**
最常用的Map,它根据键的HashCode 值存储数据,根据键可以直接获取它的值，具有很快的访问速度。**HashMap最多只允许一条记录的键为Null(多条会覆盖);**允许多条记录的值为 Null。非同步的。
**TreeMap**
能够把它保存的记录根据键(key)排序,默认是按升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的。TreeMap不允许key的值为null。非同步的。  
**Hashtable**
与 HashMap类似**,不同的是:key和value的值均不允许为null**;它支持线程的同步，即任一时刻只有一个线程能写Hashtable,因此也导致了Hashtale在写入时会比较慢。  
**LinkedHashMap**
保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的.在遍历的时候会比HashMap慢
#### map一般方法
```java
//Map是一个独立的接口，和Collection无关。存储的数据论对。每一对称为键值对
//允许空键，空值。
//键不能重复，值可以重复。
/*
 * 给定一个键和一个值，你可以将该值存储在一个 Map 对象。之后，你可以通过键来访问对应的值。
当对象的类型和 Map 里元素类型不兼容的时候，就会抛出一个 ClassCastException 异常。
当在不允许使用 Null 对象的 Map 中使用 Null 对象，会抛出一个 NullPointerException 异常。
当尝试修改一个只读的 Map 时，会抛出一个 UnsupportedOperationException 异常。
 */
import java.util.*;
import java.util.function.BiFunction;

public class Demo {
    public static void main(String[] args) {
        //构造一个空的 HashMap ，默认初始容量（16）和默认负载因子（0.75）。
        Map<String, String> map = new HashMap<>();
        //将指定的值与该映射中的指定键相关联
        String v = map.put("name", "zhangsan");// null.返回上一次添加的相同键名的键值对的值
        // 键不能重复。lisi把之前的zhangsan覆盖了。
        String v1 = map.put("name", "lisi");
        // 返回值表明zhangsan被覆盖了
        sop(v1);// zhangsan
        System.out.println(map);// {name=lisi}
        map.put("age", "20");
        map.put("address", "shangahi");// {address=shangahi, name=lisi, age=20}
        sop(map);

        //如果指定的键尚未与值相关联（或映射到 null ）将其与给定值相关联并返回 null ，否则返回当前值。
        map.putIfAbsent("name", "liuneng");// 如果键不存在再添加。就是说不覆盖原先有的。
        sop(map);// {address=shangahi, name=lisi, age=20}
        map.putIfAbsent("sex", "male");
        sop(map);// {address=shangahi, sex=male, name=lisi, age=20}

        Map<String, String> m = new HashMap<>();
        m.put("hobby", "sleep");
        m.put("weight", "80");
        map.putAll(m);// 把接收集合中的所有的键值对添加到当前集合中
        sop(map);// {address=shangahi, sex=male, name=lisi, weight=80, age=20, hobby=sleep}

        //依据键删除键值对。键是唯一的
        map.remove("hobby");
        sop(map);// {address=shangahi, sex=male, name=lisi, weight=80, age=20}

        // 只有键和值都匹配时才会删除
        map.remove("weight", "80");
        sop(map);// {address=shangahi, sex=male, name=lisi, age=20}
        // map.clear();//清空所有

        //replace(K key, V value)
        //Replaces the entry for the specified key only if it is currently mapped to some value.
        // 第一个参数表明替换的是哪个键值对，第二个为替换后的值
        String v2 = map.replace("sex", "female");
        sop(v2);// male 返回被替换的值
        sop(map);// {address=shangahi, sex=female, name=lisi, age=20}

        //replace(K key, V oldValue, V newValue)
        map.replace("age", "20", "21");// 键值对一致再修改
        sop(map);// {address=shangahi, sex=female, name=lisi, age=21}

        // R applt(T t,U u)方法的返回值替换原来的值
        // 依据一定的自定义规则取替换，参数t接收key，u接收value。
        // 比如可以把已ad开头的键都替换了
        //将每个条目的值替换为对该条目调用给定函数的结果，直到所有条目都被处理或该函数抛出异常
        map.replaceAll((t, u) -> {
            if ("name".equals(t)) {
                return u + "ok";
            }
            return u;
        });
        sop(map);// {address=shangahi, sex=female, name=lisiok, age=21}

        map.replaceAll(new BiFunction<String, String, String>() {
            @Override
            public String apply(String s, String s2) {
                return s2+"ok";
            }
        });
        sop("map");

        //根据键获取值
        //返回到指定键所映射的值
        sop(map.get("address"));//shangahi
        //如果集合中不存在该key，就用现在写的这个180。如果存在，等同于上面的get方法
        sop(map.getOrDefault("height","180"));//180
        //该key存在时，返回null
        sop(map.get("dirty"));//bull

        //返回此地图中键值映射的数量
        sop(map.size());//4

        //返回此map中包含的值的Collection视图
        //Returns a Collection view of the values contained in this map.
        Collection s=map.values();
        sop(s);//[shangahi, female, lisiok, 21]

        //判断
        sop(map.isEmpty());//fasle

        sop(map.containsKey("sex"));//true

        sop(map.containsValue("female"));//true
    }
    public static void sop(Object obj) {
        System.out.println(obj);
    }
}
```
#### python dict
```python
dict = {'Alice': '2341', 'Beth': '9102', 'Cecil': '3258'}
print(dict['Alice'])#2341
print(dict.get("Alice"))#2341
#返回指定键的值，如果值不在字典中返回default值
print(dict.get("liming","89"))#89
#删除元素
del dict["Alice"]
print(dict) #{'Beth': '9102', 'Cecil': '3258'}
print(len(dict))#2
#输出字典可打印的字符串表示。
print(str(dict)) #{'Beth': '9102', 'Cecil': '3258'}
#以列表返回可遍历的(键, 值) 元组数组
print(dict.items())#dict_items([('Beth', '9102'), ('Cecil', '3258')])
#以列表返回一个字典所有的键
print(dict.keys())#dict_keys(['Beth', 'Cecil'])
#以列表返回字典中的所有值
print(dict.values())#dict_values
#判断是否包含某个键
print(dict.__contains__("Beth")) #Ttue
```
#### Map.keySet
```java
//Map的迭代。keySet得到所有的键，并返回到该集合。Set有迭代器。之后可以利用键获取Value。
import java.util.HashMap;
import java.util.Map;
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        // Map集合没有迭代器
        Map<String, String> map = new HashMap<>();
        map.put("name", "zhangsan");
        map.put("age", "20");
        map.put("address", "shangahi");

        //Returns a Set view of the keys contained in this map.
        //这个set是hashset还是treeset？这个Set接口的实现类是HashMap中的内部类KeySet！！
        //  将map中所有的键存入到Set集合，因为set具备迭代器，所有迭代方式取出所有的键,再根据get()方法  ，获取每一个键对应的值
        Set<String> keys = map.keySet();
        Iterator<String> ite = keys.iterator();
        while (ite.hasNext()) {
            //迭代键
            String key = ite.next();
            //依据键获取值。
            String value = map.get(key);
            System.out.println(key + "=" + value);
        }
    }
}
```


#### map.entrySet()遍历
```java
//每个键值对对应一个映射关系类型的数值，Map.Entry就是映射关系类型
//得到每个键值对映射关系的数据，存到Set集合中并返回该集合。使用迭代器迭代该集合，每次遍历出的是一个Map.Entry类型的数据。从该数据中既能得到键，也能得到值。
//Entry是定义在Ｍａｐ内部的一个静态接口．Ｅｎｔｒｙ是对ｍａｐ内部键值对映射关系的描述，所以定义在ｍａｐ内部。
import java.util.HashMap;
import java.util.Map;
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        // Map集合没有迭代器
        Map<String, String> map = new HashMap<>();
        map.put("name", "zhangsan");
        map.put("age", "20");
        map.put("address", "shangahi");
        //map.entrySet()Returns a Set view of the mappings contained in this map.
        //返回此地图中包含的映射的Set视图
        Set<Map.Entry<String,String>> sets=map.entrySet();

        Iterator<Map.Entry<String,String>> ite = sets.iterator();
        while (ite.hasNext()) {
            //Map.Entry为映射关系类型
            Map.Entry<String,String> en = ite.next();
            //getkey方法获取key。返回与此条目相对应的键。
            String key=en.getKey();
            //getValue方法获取值,返回与此条目相对应的值
            String value=en.getValue();
            System.out.println(key + "=" + value);
        }
    }
}
```
#### 集合都可以用forEach方法遍历
包括Collection与Map
```java
//利用lmabda进行遍历
import java.util.HashMap;
import java.util.Map;
import java.util.*;
import java.util.function.BiConsumer;

public class Demo {
    public static void main(String[] args) {
        // Map集合没有迭代器
        Map<String, String> map = new HashMap<>();
        map.put("name", "zhangsan");
        map.put("age", "20");
        map.put("address", "shangahi");
        Set<String> keys = map.keySet();
        //用增强for循环遍历
//      for(String key:keys) {
//        System.out.println(key+"="+map.get(key));
//      }

        //利用forEach实现遍历，应该是最简单的写法
        //对此映射中的每个条目执行给定的操作，直到所有条目都被处理或操作引发异常。
        map.forEach((t,u)->System.out.println("key:"+t+" "+"value:"+u));
        
        //其实集合都有很简单的遍历方式，比如下面这个list
        //那么什么时候用lambda遍历比较好呢？如果对每个对象的处理不是很复杂，就可以用这个
        Collection<Integer> s=new ArrayList<>();
        s.add(1);
        s.add(2);

        s.forEach(System.out::println);
        
        map.forEach(new BiConsumer<String, String>() {
            @Override
            public void accept(String s, String s2) {
                System.out.println(s+":"+s2);
            }
        });
    }
}
```


#### List转Array
想限制不能随便修改集合长度
```java
//list转数组
import java.util.*;
public class Demo10 {
	public static void main(String[] args) {
		
		//数组转集合
		List<String> list =new ArrayList<>();
		list.add("a");
		list.add("b");
		list.add("c");
		
		//String[] y = x.toArray(new String[0]);
		//Returns an array containing all of the elements in this list inproper sequence 
		//给定数组长度大于等于集合的大小，会使用给定的数组
		//给定数组长度小于集合的大小，不会使用给定的数组
		String[] arr=list.toArray(new String[5]);
		System.out.println(arr.length);
	}
}

```
#### Array转LIst
数组想使用集合的丰富方法
```java
//数组想使用集合的丰富方法
import java.util.*;
public class Demo9 {
	public static void main(String[] args) {
		
		//数组转集合，方便使用集合的丰富的方法		
		String[] arr= {"a","b","c"};
		List<String>  list=Arrays.asList(arr);
		//比如，数组并没有contains方法。使用数组判断包含需要遍历
		System.out.println(list.contains("a"));//true 修正之前笔记		
		
	}
}

```
#### 每个班有多个学生的 map实现
```java
//每个班有多个学生的 map实现
import java.util.*;

public class Demo8 {
	public static void main(String[] args) {
		// 每个班有多个学生.key 班级 值学生列表

		Map<String, List<Student>> school = new HashMap<>();

		List<Student> list1 = new ArrayList<>();
		list1.add(new Student("lisi", 20));
		list1.add(new Student("zhangsan", 20));
		list1.add(new Student("xiaobai", 20));
		list1.add(new Student("liuqian", 27));
		list1.add(new Student("xiaobai", 20));

		List<Student> list2 = new ArrayList<>();
		list2.add(new Student("lisi", 20));
		list2.add(new Student("zhangsan", 20));
		list2.add(new Student("xiaobai", 20));
		list2.add(new Student("liuqian", 27));
		list2.add(new Student("xiaobai", 20));

		school.put("bigdata", list1);
		school.put("h5", list2);

		Set<String> keys = school.keySet();
		Iterator<String> ite = keys.iterator();
		while (ite.hasNext()) {
			String banji = ite.next();
			System.out.println(banji);
			System.out.println("该班学员");
			List<Student> lists = school.get(banji);
			lists.forEach(t -> System.out.println(t));
		}
	}
}

class Student {
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	String name;
	int age;

	public Student(String name, int age) {
		super();
		this.name = name;
		this.age = age;
	}

	public int compareTo(Student stu) {
		int num = this.age - stu.age;

		return num == 0 ? this.name.compareTo(stu.name) : num;
	}

	public boolean equals(Object obj) {
		if (!(obj instanceof Student))
			throw new ClassCastException();
		Student stu = (Student) obj;
		return this.name.equals(stu.name) && this.age == stu.age;
	}

	public String toString() {
		return name + age;
	}

}
```
### HashMap
#### HashMap利用hascode以及equals对key进行去重
```java
//HashMap保证键唯一的底层原理
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        // 什么时候使用映射：存在映射关系
        // 一个学生对于一个地址
        // 保证键不重复的原理和HashSet一样 int hashCode、 boolean equals
        //key是可以为任意Object的
        HashMap<Student, String> map = new HashMap<>();
        map.put(new Student("lisi", 20), "beijing");
        map.put(new Student("zhangsan", 20), "beijing");
        map.put(new Student("xiaobai", 20), "beijing");
        map.put(new Student("liuqian", 27), "beijing");
        map.put(new Student("xiaobai", 20), "shenzhen");
        //key看出最后一个xiaobai只进去了一个
        //如果不定义hashcode和equals方法，结果是{liuqian27=beijing, zhangsan20=beijing, lisi20=beijing, xiaobai20=beijing, xiaobai20=shenzhen}
        //可以看到xiaobao有两个，因为两个key都是new的，肯定不是同一对象
        System.out.println(map);//{lisi20=beijing, zhangsan20=beijing, xiaobai20=shenzhen, liuqian27=beijing}

        //遍历输出
        map.forEach((t,u)-> System.out.println(t+":"+u));
    }
}
class Student {
    String name;
    int age;
    public Student(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }
    public int hashCode() {
        return name.hashCode() + age * 36;
    }
    // 姓名年龄相同的认为是相同的键
    public boolean equals(Object obj) {
        if (!(obj instanceof Student))
            throw new ClassCastException();
        Student stu = (Student) obj;
        return this.name.equals(stu.name) && this.age == stu.age;
    }
    public String toString() {
        return name + age;
    }
}
```
### TreeMap
#### TreeMap利用Comparable接口orcomparator实现key去重
```java
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        // 和treeset一样，key排序的，根据键排序。排序方式两种Comparable与comparator
        //TreeMap的构造函数是有一个带参数的
        //这个写法是可以简化的
        TreeMap<Student, String> map = new TreeMap<>(new CombyName());
        map.put(new Student("lisi", 20), "beijing");
        map.put(new Student("zhangsan", 20), "beijing");
        map.put(new Student("xiaobai", 20), "beijing");
        map.put(new Student("liuqian", 27), "beijing");
        map.put(new Student("xiaobai", 20), "shenzhen");

        //由于重写了Comparator接口接口，所以数据进入map的顺序会依据自定义的顺序，也就是Comparator接口中compare方法写的顺序
        System.out.println(map);//{lisi20=beijing, liuqian27=beijing, xiaobai20=shenzhen, zhangsan20=beijing}
    }
}
//自定义实现Comparator接口。这个其实可以用匿名实现
class CombyName implements Comparator<Student> {
    public int compare(Student stu1, Student stu2) {
        //我们自定义根据名称来排序，也就是说map中的元素是以如下顺序进入
        return stu1.getName().compareTo(stu2.getName());
    }
}
class Student {
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    String name;
    int age;
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    //对Student类实现Comparable接口，这样treemap中的key排序就有了依据
    public int compareTo(Student stu) {
        int num = this.age - stu.age;
        return num == 0 ? this.name.compareTo(stu.name) : num;
    }
    public boolean equals(Object obj) {
        if (!(obj instanceof Student))
            throw new ClassCastException();
        Student stu = (Student) obj;
        return this.name.equals(stu.name) && this.age == stu.age;
    }
    public String toString() {
        return name + age;
    }
}
```
**简化treemap构造函数的写法**
```java
TreeMap<Student, String> map = new TreeMap<>(new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                return o1.getName().compareTo(o2.getName());
            }
        });
```
还可以进一步简化，
```java
 //构造一个新的，空的树图，按照给定的比较器排序。 
        TreeMap<Student, String> map = new TreeMap<>((o1, o2) -> o1.getName().compareTo(o2.getName()));
```
还可以再再简化，所以说，treeset中传入一个自定义比较器其实蛮easy
```java
TreeMap<Student, String> map = new TreeMap<>(Comparator.comparing(Student::getName));
```
### 学习工具类-Collections
```java
//学习阅读jdk文档
import java.util.*;
import java.util.Collections;
public class Demo {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        //将所有指定的元素添加到指定的集合。 要添加的元素可以单独指定或作为数组指定
        Collections.addAll(list, 12, 3, 45, 6, 7, 8);
        System.out.println(list);// [12, 3, 45, 6, 7, 8]
        
        
        Collections.reverse(list);
        System.out.println(list);// [8, 7, 6, 45, 3, 12]
        
        //交换指定列表中指定位置的元素
        Collections.swap(list, 0, 3);
        System.out.println(list);// [45, 7, 6, 8, 3, 12]
       
        // 随机排列
        //使用默认的随机源随机排列指定的列表。 所有排列都以大致相等的可能性发生。
        Collections.shuffle(list);
        System.out.println(list);// [45, 12, 8, 3, 7, 6]
        
        
        // 排序
        Collections.sort(list);
        System.out.println(list);// [3, 6, 7, 8, 12, 45]
        
        
        // static <T extends Object & Comparable<? super T>>
        // 集合中的类必须实现Comparable接口才能用这个max方法
        // Integer是实现了comparable接口的
        Integer max = Collections.max(list);
        System.out.println(max);// 45
        
        
        // 自定义比较方法~
        Integer t = Collections.max(list, (t1, t2) -> t2 - t1);
        System.out.println(t);// 3
        
        
        
        
        // <T> Comparator<T> reverseOrder(Comparator<T> cmp)
        // 返回和给定方式相反的比较方式 .返回的是一个比较器
        max = Collections.max(list, Collections.reverseOrder((t1, t2) -> t2 - t1));
        System.out.println(max);// 45
        List<String> list2 = new ArrayList<>();
        Collections.addAll(list2, "libai", "dufu", "wangwei");
        String mmax = Collections.max(list2);
        System.out.println(mmax);// wangwei
        //默认比较与自定义比较
        Collections.sort(list2);
        System.out.println(list2);// [dufu, libai, wangwei]
        Collections.sort(list2, Collections.reverseOrder());
        System.out.println(list2);// [wangwei, libai, dufu]
        // 返回和默认的比较方式相反的比较方式
        mmax = Collections.max(list2, Collections.reverseOrder());
        System.out.println(mmax);// dufu


        //集合之间的拷贝
        List<String> list3 = new ArrayList<>();
        Collections.addAll(list3, "", "", "");
        //只负责拷贝，不负责开辟内存
        Collections.copy(list3, list2);
        System.out.println(list3);//[wangwei, libai, dufu]
    }
}
```
### LinkedHashSet & LinkedHashMap
```java

//LinkedHashSet & LinkedHashMap
import java.util.*;

public class Demo12 {
	public static void main(String[] args) {
		LinkedHashSet<String> ls = new LinkedHashSet<>();
		ls.add("hello");
		ls.add("bigdata");
		ls.add("sql");
		// 同添加顺序一致 链表加哈希表实现的
		// 还是不可以重复，Set

		System.out.println(ls);// [hello, bigdata, sql]

		// 无序，不重复用HashSet 有序不可重复 用LinkedHashSet
		// 有序可重复性 ArrayList LinkedList

		LinkedHashMap<String, String> lm = new LinkedHashMap<>();
		lm.put("name", "lisi");
		lm.put("age", "22");
		lm.put("sex", "male");
		System.out.println(lm);// {name=lisi, age=22, sex=male}

		// 得到线程安全的各种集合对象
		List<String> list = Collections.synchronizedList(new ArrayList<String>());

	}
}

```
### Properities
#### 从java获取系统属性
```java
//从java获取系统属性
import java.util.*;
public class Demo {
    public static void main(String[] args) {
        //Properties类表示一组持久的属性。 
        // Properties可以保存到流中或从流中加载。 属性列表中的每个键及其对应的值都是一个字符串。
        // Properties是Map的子类
        Properties pro = System.getProperties();
        
        // 得到all key的集合最简单的方式
        //返回此属性列表中的一组键，其中键及其对应的值为字符串
        Set<String> keys = pro.stringPropertyNames();
        
        //pro.getProperty()使用此属性列表中指定的键搜索属性
        keys.forEach(t -> System.out.println(t+pro.getProperty(t)));
       
       
        // 修改或者添加，等价于map中的put
        // 改的是集合，并非系统中的变了
        pro.setProperty("user.name", "hmj");
        // 查看修改后的结果
        for (String key : keys) {
            // getProperty获取值
            String value = pro.getProperty(key);
            System.out.println(key + ":" + value);
        }
    }
}
```


# 
# 引申
#### 利用map进行数组去重
```java
//利用Map对数组进行去重操作

import java.util.*;
public class Demo2 {
	public static void main(String[] args) {
		int[] arr= {1,2,3,4,5,3,3,4,5};
		//创建map把唯一的值放进去作为key，至于value是什么则无关紧要
		Map<Integer,Object> map=new HashMap<>();
		for (int i = 0; i < arr.length; i++) {
			map.put(arr[i], true);
		}
		System.out.println(map);
		//取出所有的key
		Set<Integer> set=map.keySet();
		Integer[] arr1=new Integer[set.size()];
		//用toArray方法转为Array，注意这个破方法有个参数
		arr1=set.toArray(arr1);
		System.out.println(Arrays.toString(arr1));	
		
	}
}

```


