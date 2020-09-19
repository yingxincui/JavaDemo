# 0827 lambda 与集合学习笔记

#  lambda 
#### 入门
```java
//lambda表达式，只能用于接口 jdk 1.8后启用
class Demo6 {
	public static void main(String[] args) {
		// 匿名内部类
		inter in = new inter() {
			public int add(int a, int b) {
				return a + b;
			}
		};
		int sum = in.add(5, 5);
		System.out.println(sum);// 10

		// lambda 匿名函数
		inter tt = (a, b) -> {
			return a + b;
		};
		System.out.println(tt.add(5, 5));
	}
}

interface inter {
	int add(int a, int b);
}
```
#### lambda是为了简化接口的实现
lambda
```java
public class Demo {
	public static void main(String[] args) {
		
		inter in=((a,b)->a+b);
		//java的lmabda表达式是要通过对象去调用的。说匿名，但其实名字在接口里面。
		//python的lambda是真正的匿名。
		System.out.println(in.add(2, 3));
	}	
}

interface inter{
	int add(int a,int b);
}
```
```java
public class Demo {
	public static void main(String[] args) {
		// 写起来比lambda麻烦了许多，理解上也不如lambda直观。所以说lambda还是有其特点。
		inter in = new inter() {
			public int add(int a, int b) {
				return a + b;
			}
		};
		// 调用
		System.out.println(in.add(2, 3));// 5
	}
}

interface inter {
	int add(int a, int b);
}
```


```java
public class Demo {
	public static void main(String[] args) {
		//既然有现成的功能相同的，就不妨借用一下
		//可以算作对lambda的进一步简化
		inter in=Add::add;
		System.out.println(in.add(2, 3));//5
	}
}

class Add{
	public static int add(int a,int b) {
		return a+b;	
	}
}
interface inter {
	int add(int a, int b);
}
```


#### lambda使用的前提条件-函数式接口
```java
//判断函数式接口
class Demo6 {
	public static void main(String[] args) {

		// lambda 匿名函数
		// 使用前提 必须是接口，并且接口中有且只有一个必须被重写的抽象方法
		inter tt = (a, b) -> {
			return a + b;
		};
		System.out.println(tt.add(5, 5));

	}
}

//可以使用lambda表达式的接口称为函数式接口
//有且只有一个必须被重写方法的接口就是函数式接口

interface inter {
	int add(int a, int b);
}

// 如下为函数式接口，一个已经实现的不算~ 因为这个可以不重写
interface Test5 {
	int tests();

	static void show() {
		System.out.println("hellow world");
	}
}

//是函数式接口
@FunctionalInterface
interface Test6 {
	int tests();

	default void fun() {
		System.out.println("good");
	}
}

//不是函数式接口,没有需要被重写的
interface Test7 {

}

@FunctionalInterface // 用于检测是否函数式接口
//不是函数式接口，toString继承了object中已经实现的。不是必须重新实现。
interface Test8 {
	String toString();
}

```


```java
//函数式接口实操
/*java8中一个非常重要的特性就是lambda表达式，我们可以把它看成是一种闭包，
 * 它允许把函数当做参数来使用，是面向函数式编程的思想，一定程度上可以使代码看起来更加简洁
 Java 是面向对象语言，除了原始数据类型之处，Java 中的所有内容都是一个对象。
 而在函数式语言中，我们只需要给函数分配变量，并将这个函数作为参数传递给其它函数就可实现特定的功能。
 JavaScript 就是功能编程语言的典范（闭包）
 在其它语言中，Lambda 表达式的类型是一个函数；
 但在 Java 中，Lambda 表达式被表示为对象，因此它们必须绑定到被称为功能接口的特定对象类型。
 */
class Demo6 {
	public static void main(String[] args) {
		// 无参无返回值实现
		// 箭头前面参数 后面方法体
		NoneReturnNoneParam noneReturnNoneParam = () -> {
			System.out.println("无参无返回值");
		};
		noneReturnNoneParam.test();

		NoneReturnOneParam noneReturnOneParam = (int a) -> {
			System.out.println(a);
		};
		noneReturnOneParam.test(1);

		NoneReturnMoreParam noneReturnMoreParam = (int a, int b) -> {
			System.out.println(a + b);
		};
		noneReturnMoreParam.test(1, 2);

		ReturnNoneParam returnNoneParam = () -> {
			return 6;
		};
		int n = returnNoneParam.test();
		System.out.println(n);

		ReturnOneParam returnOneParam = (int a) -> {
			return a;
		};
		int n1 = returnOneParam.test(8);
		System.out.println(n1);

		ReturnMoreParam returnMoreParam = (int a, int b) -> {
			return a + b;
		};
		int n2 = returnMoreParam.test(2, 9);
		System.out.println(n2);

	}
}

interface NoneReturnNoneParam {
	void test();
}

interface NoneReturnOneParam {
	void test(int a);
}

interface NoneReturnMoreParam {
	void test(int a, int b);
}

interface ReturnNoneParam {
	int test();
}

interface ReturnOneParam{
	int test(int a);
}

interface ReturnMoreParam{
	int test(int a,int b);
}


```
#### lambda表达式的简化
```java
//lamda表达式的省略
class Demo6 {
	public static void main(String[] args) {

		NoneReturnNoneParam noneReturnNoneParam = () -> {
			System.out.println("无参无返回值");
		};
		noneReturnNoneParam.test();
		// 参数的类型可以省略
		// 如果只有一个参数，参数的小括号可以省略
		// 如果大括号只有一条语句，那么大括号也可以省略
		NoneReturnOneParam noneReturnOneParam = a -> System.out.println(a);

		noneReturnOneParam.test(1);

		// 数据类型要省略，必须都省略
		NoneReturnMoreParam noneReturnMoreParam = (a, b) -> System.out.println(a + b);
		noneReturnMoreParam.test(1, 2);

		ReturnNoneParam returnNoneParam = () -> 6;
		int n = returnNoneParam.test();
		System.out.println(n);

		// 大括号只有一条语句，并且这条语句是返回语句，return可以省略。花括号也要省略
		ReturnOneParam returnOneParam = (a) -> a;
		int n1 = returnOneParam.test(8);
		System.out.println(n1);

		ReturnMoreParam returnMoreParam = (a, b) -> a + b;
		int n2 = returnMoreParam.test(2, 9);
		System.out.println(n2);

	}
}

interface NoneReturnNoneParam {
	void test();
}

interface NoneReturnOneParam {
	void test(int a);
}

interface NoneReturnMoreParam {
	void test(int a, int b);
}

interface ReturnNoneParam {
	int test();
}

interface ReturnOneParam {
	int test(int a);
}

interface ReturnMoreParam {
	int test(int a, int b);
}

```
#### lambda借用现成的static方法
```java

//静态方法引用：接口中的方法参数和返回值 和某个静态方法的参数和返回值类型完全一样
public class Demo7 {
	public static void main(String[] args) {
//		inter in=(a,b)->a*b;
//		int s=in.cal(2, 3);
//		System.out.println(s);
		
		//静态方法引用
		//实际执行的是caculate方法
		//在这些情况下，通过名称引用现有的方法，通常能更直白的表现出方法的调用过程。
		//对于已经存在的且具有方法名称的方法，它其实是简洁且易于读取的一种lambda表达式，或者说是对lambda表达式的一种进一步简化
		inter in=Caculate::caculate;
		System.out.println(in.cal(2, 3));
				
	}
}

class Caculate{
	public static int caculate(int a ,int b) {
		return a*b;
	}
}


interface inter{
	int cal(int a,int b);
}
```
#### lambda借用现成的non static方法
```java
//非静态方法引用
public class Demo8 {
	public static void main(String[] args) {
		//::代表引用。cal方法引用caculate方法的功能
		//接口实例对象引用现存的另一个方法
        //等价于引用new Caculates()对象的caculate方法
		inter1 in=new Caculates()::caculate;
		System.out.println(in.cal(3, 4));//12
	}
}

class Caculates{
	public  int caculate(int a ,int b) {
		return a*b;
	}
}


interface inter1{
	int cal(int a,int b);
}
```
#### lambda借用现成的构造方法
```java
public class Demo {
	public static void main(String[] args) {
		
		//借用一下Person类的new方法
		//既然我要实现的方法在Person的构造方法里有，那么借用构造方法也是可以的。
		inter in=Person::new;
		//这部分属于调用了。
		Person p=in.test("xioaming",18);
		System.out.println(p);//xioaming18
	
	}
}


interface inter {
	//该接口的目的是返回一个Person对象
	Person test(String name,int age);
}

class Person {

    private String name;
    private int age;
    Person(){}
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String toString() {
        return name + age;
    }
}
```


如果不借用，常规写法就比较麻烦了
```java
public class Demo {
	public static void main(String[] args) {
		
		//inter in=Person::new;
		//不用lambda按最常规写法就是下面这样。
		inter in=new inter() {
			//重写接口中的test方法
			public Person test(String name,int age) {
				return new Person(name,age);
			}
		};	
		//调用接口对象
		Person p=in.test("xioaming",18);
		System.out.println(p);//xioaming18
	
	}
}


interface inter {
	//该接口的目的是返回一个Person对象
	Person test(String name,int age);
}

class Person {

    private String name;
    private int age;
    Person(){}
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String toString() {
        return name + age;
    }
}
```
#### 利用lambda进行遍历
```java
import java.util.*;
public class Demo1 {
	public static void main(String[] args) {
		// 定义字符串数组
		String[] s = { "apple", "orange", "banana", "pear" };
		// 这个数组转List功能不是很好用
		// 如果你的List只是用来遍历，就用Arrays.asList()。
		// 如果你的List还要添加或删除元素，还是乖乖地new一个java.util.ArrayList，然后一个一个的添加元素
		List<String> fruit = Arrays.asList(s);
		System.out.println(fruit);// [apple, orange, banana, pear]

		// 使用增强for循环遍历
		for (String f : fruit) {
			System.out.print(f + " ");// apple orange banana pear
		}

		// 使用lambda表达式
		// 用来lambda的地方，写起来必定比其他方式简单。
		fruit.forEach(f -> System.out.print(f + " "));// apple orange banana pear

		// 使用迭代遍历
		Iterator ite = fruit.iterator();
		while (ite.hasNext()) {
			System.out.println();
		}
	}
}
```
#### 利用Lambda进行排序
```java
//利用lambda对数组进行排序
import java.util.*;

public class Demo1 {
	public static void main(String[] args) {
		// 定义字符串数组
		String[] fruit = { "apple", "orange", "banana", "pear" };
		// 这个数组转List功能不是很好用
		// 如果你的List只是用来遍历，就用Arrays.asList()。
		// 如果你的List还要添加或删除元素，还是乖乖地new一个java.util.ArrayList，然后一个一个的添加元素

		System.out.println(fruit);// [apple, orange, banana, pear]

		// <Object> void java.util.Arrays.sort(Object[] a, Comparator<? super Object> c)
		// 一般实现方法
		// Comparator是接口名，方法帮助可以看到
		// compare要重写的方法，这个看帮助文档
		Arrays.sort(fruit, new Comparator<String>() {
			@Override
			public int compare(String s1, String s2) {
				return (s1.compareTo(s2));
			}
		});

		// lambda实现.lambda作为sort函数的一个参数
		// lambda作为参数时，隐藏了接口的名称。Comparator
		// lambda作为参数时，隐藏了接口待实现函数的名称compare。
		Arrays.sort(fruit, (String s1, String s2) -> (s1.compareTo(s2)));
		System.out.println(Arrays.toString(fruit));// [apple, banana, orange, pear]
	}
}

```
#### 特殊引用
引用  lambda
```java
package com.qianfeng.Day17;

//特殊引用
//接口的方法的参数是引用类型（需要接收一个对象）方法体是调用接收对象的某个方法
public class Demo {
	public static void main(String[] args) {
		// 一般lambda实现方式
		Test1 test = (person) -> person.getName();
		String str1 = test.getInfo(new Person("老王", 60));
		System.out.println(str1);// 老王

		// 特殊引用写法
		// 这种写法和上面写发实现的效果完全一样
		Test1 tests = Person::getName;
		String str = tests.getInfo(new Person("小白", 18));
		System.out.println(str);// 小白

		// 特殊引用写法
		Test2 test2 = Person::show;
		test2.showInfo(new Person("刘总", 45));

		// 一般写法
		// 参数是引用类型，多出来的参数name是调用Person对象的某个方法需要的数据，满足该条件也可以
		// Test3 test3=(person,name)->person.setName(name);
		// test3.showInfo(person, name);

		// 特殊引用写法
		Test3 test3 = Person::setName;
		test3.setInfo(new Person("小刘", 80), "小刘");

	}
}

class Person {
	private String name;
	private int age;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String toString() {
		return name + age;
	}

	public void show() {
		System.out.println("show");// show
	}
}

interface Test1 {
	String getInfo(Person person);
}

interface Test2 {
	void showInfo(Person person);
}

interface Test3 {
	void setInfo(Person person, String name);
}

```


# 集合
集合
![](https://cdn.nlark.com/yuque/0/2020/png/2145106/1598695206477-4b619902-d3ee-4f76-83d3-389ff1bc8fc0.png)#### collection不能操作下标
```java

/*
 * 存储不同类型的对象
 * Object[] arr=new Object[200]
 * 数组长度固定，不利于添加和删除
 * 集合：可以存储不同类型的对象
 * 随着添加的对象的个数的增加，集合的容量会自动增加~
 * 集合只能存储引用类型对象
 * 集合提供了丰富的方法，便于实现各种功能 
 * 最顶层的接口是Collection，其中定义的是子类均可以用的~
 * 
 */
//集合增删查改功能演示
import java.util.*;

public class Demo1 {
	public static void main(String[] args) {
		Collection col = new ArrayList();
		
		
		// 添加功能
		col.add("java01");
		col.add("java02");
		col.add("java03");
		// col.add(666);
		sop(col);// [java01, java02, java03, 666]

		Collection col2 = new ArrayList();
		col2.add("big data");
		col2.add("h5");

		col.addAll(col2);
		sop(col);// [java01, java02, java03, 666, big data, h5]

		
		//判断
		
		boolean b=col.contains("java01");
		System.out.println(b);//true
		//判断col1集合是否包含col2集合
		boolean c=col.containsAll(col2);
		System.out.println(c);//true
		//判断集合是否为空
		boolean d=col.isEmpty();
		System.out.println(d);//false		
		//判断集合是否一样~重写了Object的equals方法，比较集合中的对象是否完全一样
		boolean e=col.equals(col2);
		System.out.println(e);//false
		
		//计数
		
		sop(col.size());//5
		
		
		// 删除功能
		
		col.remove("java01");
		sop(col);// [java02, java03, 666, big data, h5]
		// t接收的是集合中的每个对象,t依次接收集合中的每个对象
		// 参数为lambda表达式，其返回值类型为boolean类型
		// 删掉集合中以java开头的元素
		// String用于把Object类型转为String类型，以便调用startsWith方法
		col.removeIf(t -> ((String) t).startsWith("java"));
		sop(col);// [big data, h5]

		// 从col中删除和col2中相同的对象，就是删除交集了。
		// col.removeAll(col2);
		// sop(col);//[java02, java03, 666]
		// 保留交集
		col.retainAll(col2);
		sop(col);// [big data, h5]
		col.clear();
		sop(col);// []	
	}

	
	public static void sop(Object obj) {
		System.out.println(obj);
	}
}

```
#### 集合的三种遍历方式
```java
package com.qianfeng.Day17;

//集合的遍历 3种方法
//Iterator iterator() 返回此集合中元素的迭代器
//集合是使用内部类实现遍历的，所以 Itreator接口是对不同类型集合中内部类提取共性得到的一个接口
//所以内部类都实现了Itreator接口，Iterator方法返回内部类对象，因为内部类实现了Iterator接口
import java.util.*;

public class Demo1 {
	public static void main(String[] args) {
		Collection col = new ArrayList();// 多态

		col.add("java01");
		col.add("java02");
		col.add("java03");
		col.add(666);

		// 用迭代器遍历
		//集合的iterator方法
		Iterator ite = col.iterator();
		while (ite.hasNext()) {
			Object obj = ite.next();
			System.out.println(obj);
		}
		
		
		//用foreach方法遍历、
		
		//集合的foreach方法，参数为lambda表达式
		//箭头前t为参数，t代表集合中的每个元素。 箭头右面为方法体
		//t就是个代号，可以随便写，比如你写个f
		
		col.forEach(t-> System.out.println(t) );

		
		//用增强for循环遍历
		for(Object obj:col) {
			System.out.println(obj);
		}
	}

}

```
#### 集合转数组-防止修改
```java
//集合转数组
//为了防止用户随意增删数据，可以转数组
import java.util.ArrayList;
import java.util.Collection;

public class Demo2 {
	public static void main(String[] args) {
		Collection col = new ArrayList();// 多态

		col.add("java01");
		col.add("java02");
		col.add("java03");
		
		Object[] arr=col.toArray();
		System.out.println(arr.length);//3
		
	}
}

```






### List
#### 比起集合，List可以操作下标
```java
import java.util.List;
import java.util.ArrayList;
import java.util.Collection;

/*
 * List
 * 存储的数据是有序的（集合中 存储数据的顺序和用户添加数据顺序一致)，存储的数据可以重复
 * 

 */
public class Demo3 {
	public static void main(String[] args) {
		List col = new ArrayList();// 多态

		col.add("java011");
		col.add("java0222");
		col.add("java033333");
		col.add("java03");
		System.out.println(col);//[java01, java02, java03, java03]
		
		//特有功能 都是可以操作下标
		col.add(2,"html");
		sop(col);//[java01, java02, html, java03, java03]
		
		List col2=new ArrayList<>();
		col2.add("hehe");
		col2.add("haha");
		
		col.addAll(3,col2);
		sop(col);
		
		//删除
		sop(col.remove(4));//haha
		sop(col);//[java01, java02, html, hehe, java03, java03]
		
		//修改
		col.set(0, "java0");
		sop(col);//[java0, java02, html, hehe, java03, java03]
		
		//根据下标获取对象
		Object obj=col.get(2);
		sop(obj);//html
		
		//截取集合中一部分存储到新集合
		sop(col.subList(1, 4));//[java02, html, hehe]
		sop(col);
		
		//获取下标
		int index=col.indexOf("java02");
		sop(index);//1
		
		//按字符串长短排序，从短到长排序
		col.sort((t1,t2)->((String)t1).length()-((String)t2).length());
		
		//替换集合中所接收的对象
		//该方法会把集合中的对象一次传给参数t
		col.replaceAll(t-> ((String)t).concat("bigdata"));		
		//col.replaceAll(t-> "heihei");	
		sop(col);
		
	}
	
	public static void sop(Object obj) {
		System.out.println(obj);
	}
	
}

```


# 引申
#### python lambda
```python
#在Python中，lambda的语法是唯一的。其形式如下：
#lambda argument_list: expression
#lambda函数是匿名的：所谓匿名函数，通俗地说就是没有名字的函数。lambda函数没有名字。
#由于其实现的功能一目了然，甚至不需要专门的名字来说明
f=lambda x,y,z:x+y+z;
print(f(1,2,3)) #6

#以求和为例
def sum(a,b):
    return a+b
print(sum(2,3)) #5

f=lambda a,b:a+b
print(f(2,3)) #5

```
#### python set
```python
#集合（set）是一个无序的不重复元素序列。
#可以使用大括号 { } 或者 set() 函数创建集合，注意：创建一个空集合必须用 set() 而不是 { }，因为 { } 是用来创建一个空字典。
#python会自动去除集合中的重复元素
basket={'apple','orange','apple','pear'}
print(basket) #{'apple', 'orange', 'pear'}
print('apple' in basket) #True 等价于java的contains方法
basket.add("banana")
print(basket)#{'pear', 'orange', 'banana', 'apple'}
basket.remove('pear')
print(basket) #{'orange', 'apple', 'banana'}
print(len(basket)) #3

#python可以从字符串直接转set，java无此功能，需要通过toByteArray实现类似功能
s=set('hello')
print(s)#{'h', 'l', 'o', 'e'}

#当然，list也可以转set
list=[1,2,3,4]
s1=set(list)
print(s1)#{1, 2, 3, 4}


```


#### Comparator接口用于排序
```java
//Comparator接口深入了解
//凡是比较的地方就可以用
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

public class Demo1 {
	// 定义一个简单的Dog内部类，有属性name和age；
	class Dog {
		public int age;
		public String name;

		public Dog(int age, String name) {
			super();
			this.age = age;
			this.name = name;
		}

		// 重写toString，方便打印对象
		@Override
		public String toString() {
			return "Dog [age=" + age + ", name=" + name + "]";
		}
	}

	public static void main(String[] args) {
		List<Dog> list = new ArrayList<>();

		list.add(new Demo1().new Dog(6, "dog A"));
		list.add(new Demo1().new Dog(5, "dog B"));
		list.add(new Demo1().new Dog(7, "dog C"));

		list.sort(new Comparator<Dog>() {
			@Override
			public int compare(Dog o1, Dog o2) {
				return o1.age - o2.age;
			}
		});
		System.out.println("给狗狗按年龄排序：" + list);// 给狗狗按年龄排序：[Dog [age=5, name=dog B], Dog [age=6, name=dog A], Dog
												// [age=7, name=Dog C]]

		// 当然，简便写法为
		list.sort((o1, o2) -> o1.age - o2.age);
		System.out.println("给狗狗按年龄排序：" + list);

		// 如果按姓名排序呢？
		list.sort(new Comparator<Dog>() {
			public int compare(Dog o1, Dog o2) {
				return o1.name.compareTo(o2.name);
			}
		});
		System.out.println("给狗狗按姓名排序：" + list);// 给狗狗按姓名排序：[Dog [age=6, name=dog A], Dog [age=5, name=dog B], Dog
												// [age=7, name=dog C]]

		// 当然,简便写法为
		// 这种写法比较简便。但是你得熟悉jdk中接口的名字，以及你写的方法体的返回值的类型要为int
		// 使用这个方法，默认你是知道这些东西的
		list.sort((o1, o2) -> o1.name.compareTo(o2.name));
		System.out.println("给狗狗按姓名排序：" + list);
	}
}

```




#### 理解UnaryOperator接口
```java
    
//理解接口UnaryOperator
import java.util.*;
import java.util.function.UnaryOperator;

public class Demo1 {
	
	public static void main(String[] args) {
		List<String> list=new ArrayList();
		list.add("apple");
		list.add("banana");
		list.add("pear");
		System.out.println(list);//[apple, banana, pear]
		
		//默认你知道UnaryOperator接口以及apply方法
		list.replaceAll(t->"apple");
		System.out.println(list);//[apple, apple, apple]
		
		//UnaryOperator是接口名称，需要重写的方法是apply方法
		list.replaceAll(new UnaryOperator<String>() {
			public String apply(String str) {
				return "good";
			}
		});
		System.out.println(list);//[good, good, good]			
	}
}

```
#### 理解Predicate接口
```java
//理解接口Predicate
import java.util.*;
import java.util.function.Predicate;
import java.util.function.UnaryOperator;

public class Demo1 {
	
	public static void main(String[] args) {
		List<String> list=new ArrayList();
		list.add("apple");
		list.add("banana");
		list.add("pear");
		System.out.println(list);//[apple, banana, pear]
		
		
		list.removeIf(t->t.endsWith("na"));
		System.out.println(list);//[apple, pear]
		
		//默认你知道Predicate接口以及需要重写的test方法。
		//返回值需要为布尔类型，返回true就会删除对应的元素。
		list.removeIf(new Predicate<String>() {
			public boolean test(String str) {
				if(str.endsWith("na")) {
					return true;
				}
				return false;
			}
		});
		System.out.println(list);//[apple, pear]
						
	}
}

```
