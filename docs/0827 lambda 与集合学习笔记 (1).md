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
#### 函数式接口
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
#### 引用静态方法
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
#### 引用非静态方法
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
#### 引用构造方法
```java
//构造方法引用
//函数式中方法中的返回值是一个引用类型的对象
public class Demo {
	public static void main(String[] args) {
//		//Tests t=()->new Person();
//		Tests t = Person::new;
//		Person ren=t.test();// 引用哪一个构造方法由test方法的参数决定。test方法空参，所以引用的是Person的空参构造方法
//		System.out.println(ren);

		// lamda表达式：引用Person的构造方法
		Test2 tt = Person::new;
		Person ren2 = tt.test("xiaoming", 18);
		System.out.println(ren2);
	}
}

class Person {

	private String name;
	private int age;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String toString() {
		return name + age;
	}
}

interface Tests {
	// 抽象方法 返回一个Person对象
	Person test();
}

interface Test2 {
	//// 抽象方法 返回一个Person对象
	Person test(String name, int age);
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
 在我们编程的时候，有时候需要集中存放多个数据，可以用数组来保存多个数据，但是**数组的长度是不可变的**，一旦数组的长度确定了之后就无法再改变，如果要保存可变长度的数据的话，数组肯定是不行的了。而且数组也无法保存具有一定关联的数据，比如：数学–80，英语–50。为了可以保存上面的这些信息，java提供了集合类，主要是负责保存、盛装数据的。因此集合相当于一个容器类。
### 集合框架
![](https://cdn.nlark.com/yuque/0/2020/webp/2145106/1599271751874-7ba3ccf5-42a8-4d42-97cf-64e8d0265d86.webp#align=left&display=inline&height=414&margin=%5Bobject%20Object%5D&originHeight=414&originWidth=906&size=0&status=done&style=none&width=906)
![](https://cdn.nlark.com/yuque/0/2020/webp/2145106/1599271813014-c7d4695e-cf9b-4a95-8004-8cef21add49a.webp#align=left&display=inline&height=446&margin=%5Bobject%20Object%5D&originHeight=446&originWidth=1200&size=0&status=done&style=none&width=1200)


_**Collection是最基本的集合接口**_，一个Collection代表一组Object，即Collection的元素（Elements）。一些Collection允许相同的元素而另一些不行，一些能排序而另一些不行。**Java SDK不提供直接继承自Collection的类**，Java SDK提供的类都是继承自Collection的“子接口”如List和Set。


所有实现Collection接口的类都必须提供两个标准的构造函数：无参数的构造函数用于创建一个空的Collection，有一个Collection参数的构造函数用于创建一个新的Collection，这个新的Collection与传入的Collection有相同的元素。后一个构造函数允许用户复制一个Collection。


**为什么继承Iterable接口而不继承Iterator?**
**因为Iterator接口的核心方法next()或者hasNext()是依赖于迭代器的当前迭代位置的**。 如果Collection直接实现Iterator接口，势必导致集合对象中包含当前迭代位置的数据(指针)，当集合在不同方法间被传递时，由于当前迭代位置不可预置，那么next()方法的结果会变成不可预知。 除非再为Iterator接口添加一个reset()方法，用来重置当前迭代位置。 但即使这样，Collection也只能同时存在一个当前迭代位置，而Iterable则不然，每次调用都会返回一个从头开始计数的迭代器。 多个迭代器是互不干扰的。
不同的Collection接口的子接口的实现类返回的Iterator具体类型可能不同，Array可能返回ArrayIterator，Set可能返回 SetIterator，Tree可能返回TreeIterator，但是它们都实现了Iterator接口。因此，客户端不关心到底是哪种 Iterator，它只需要获得这个Iterator接口即可，这就是面向对象的威力。


集合
#### collection的基本方法
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
import java.util.function.Predicate;

public class Demo {
    public static void main(String[] args) {
        Collection<String> col = new ArrayList<>();


        // 添加功能
        col.add("java01");
        col.add("java02");
        col.add("java03");
        // col.add(666);
        sop(col);// [java01, java02, java03, 666]
        Collection<String> col2 = new ArrayList<>();
        col2.add("big data");
        col2.add("h5");
        col.addAll(col2);
        sop(col);// [java01, java02, java03, 666, big data, h5]

        //判断

        boolean b=col.contains("java01");
        System.out.println(b);//true
        //判断col1集合是否包含col2集合
        //如果此集合包含指定集合中的所有元素，则返回true
        boolean c=col.containsAll(col2);
        System.out.println(c);//true
        //判断集合是否为空
        boolean d=col.isEmpty();
        System.out.println(d);//false
        //判断集合是否一样~重写了Object的equals方法，比较集合中的对象是否完全一样
        boolean e=col.equals(col2);
        System.out.println(e);//false

        //计数
        //返回此集合中的元素数
        sop(col.size());//5



        // 删除功能
        //Returns true if an element was removed as a result of this call
        //删除就删除了，怎么证明删除成功呢？看返回值
        col.remove("java01");
        sop(col);// [java02, java03, 666, big data, h5]
       
        // t接收的是集合中的每个对象,t依次接收集合中的每个对象
        // 参数为lambda表达式，其返回值类型为boolean类型
        // 删掉集合中以java开头的元素
        //默认实现使用其iterator()遍历集合的所有元素。 
        // 使用Iterator.remove()删除每个匹配元素。 
        // 如果集合的迭代器不支持删除，那么UnsupportedOperationException将被抛出。
        col.removeIf(t -> (t).startsWith("java"));
        sop(col);// [big data, h5]
        
        // 从col中删除和col2中相同的对象，就是删除交集了。
        // col.removeAll(col2);
        // sop(col);//[java02, java03, 666]
        // 保留交集
        
        //删除满足给定谓词的此集合的所有元素
        col.removeIf(new Predicate<String>() {
            @Override
            public boolean test(String s) {
                if(s.startsWith("java"))
                    return true;
                return false;

            }
        });
        sop(col);
        // 换句话说，从该集合中删除所有不包含在指定集合中的元素。
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
#### 集合的遍历
```java

//集合的遍历 3种方法
//Iterator iterator() 返回此集合中元素的迭代器
//集合是使用内部类实现遍历的，所以 Iterator接口是对不同类型集合中内部类提取共性得到的一个接口
//所以内部类都实现了Iterator接口，Iterator方法返回内部类对象，因为内部类实现了Iterator接口
import java.util.*;
import java.util.function.Consumer;

public class Demo {
    public static void main(String[] args) {
        Collection<String> col = new ArrayList<>();// 多态

        col.add("java01");
        col.add("java02");
        col.add("java03");
        //ClassCastException - 如果指定元素的类阻止将其添加到此集合
        //col.add(666);

        // 用迭代器遍历
        //集合的iterator方法
        //返回此集合中的元素的迭代器
        Iterator ite = col.iterator();
        //如果迭代具有更多的元素，则返回true 。 （换句话说，如果next()返回一个元素而不是抛出一个异常，则返回true ）
        while (ite.hasNext()) {
            //返回迭代中的下一个元素。
            //NoSuchElementException - 如果迭代没有更多的元素
            Object obj = ite.next();
            System.out.println(obj);
        }


        //用foreach方法遍历、

        //集合的foreach方法，参数为lambda表达式
        //箭头前t为参数，t代表集合中的每个元素。 箭头右面为方法体
        //t就是个代号，可以随便写，比如你写个f
        //对Iterable的每个元素执行给定的操作，直到所有元素都被处理或动作引发异常
        col.forEach(t-> System.out.println(t) );

        col.forEach(new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        });

        //用增强for循环遍历
        for(Object obj:col) {
            System.out.println(obj);
        }
    }

}

```
#### 集合变数组
```java
//集合转数组
//为了防止用户随意增删数据，可以转数组
import java.util.ArrayList;
import java.util.Collection;
public class Demo {
    public static void main(String[] args) {
        Collection col = new ArrayList();// 多态
        col.add("java01");
        col.add("java02");
        col.add("java03");
        
        //返回一个包含此集合中所有元素的数组。 如果此集合对其迭代器返回的元素的顺序做出任何保证，则此方法必须以相同的顺序返回元素
        //结果为一个包含此集合中所有元素的数组
        Object[] arr=col.toArray();
        System.out.println(arr.length);//3

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






