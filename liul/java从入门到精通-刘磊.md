# java从入门到精通

> 学习过程参考了程序员面试指南和极客时间的java核心36讲

***

## 第一节 线程

​     1.1 程序 进程 线程

​	理解线程首先要区分好程序，进程和线程的区别

​		程序：其实是一个静态的概念的概念，是应用执行的蓝本，存放在外存上，没有运行的软件叫程序(就比如我们			把QQ下载到了D盘但是并没有运行它的状态)

​		进程：用来描述程序的动态执行过程(我们运行了QQ)

​		线程：是进程中相对独立的一个程序段的执行单元（我们一边和a聊天，一边和b聊天）

​		一个进程中可以有多个线程

​	1.2 创建线程

​		1.2.1 Thread类和Runnable接口

​		创建线程有两种方法:继承Thread类或者事项Runnable接口

​    	   重写run方法，run方法就类似于主线程的main方法，run方法运行结束，其对应的线程也会结束

```java
public class xiancheng1 extends Thread{
	public static void main(String [] args){
	System.out.println("来自于主线程的输出");
	xiancheng1 xc=new xiancheng1();
	xc.start();
}
public void run(){
	System.out.println("来自于子线程的输出");
}
}
```
~~~java
public class RunTest {
public static void main(String [] args){
	//创建一个实现了Runable接口的对象
	MyThread thread=new MyThread();
	//以实现了runable接口的对象作为参数实例化Thread对象
	Thread t=new Thread(thread);
	t.start();
}
}
class MyThread implements Runnable
{
	public void run() {
		System.out.println("thread body");
	}
	
}

~~~

1.2.2 Callable和Future接口

继承Thread类或者实现Runnable接口可以完成线程的创建，在线程start后就会执行run方法中的内容。如果我们希望run方法有个返回值，我们并不能得到这个方法的返回值，并且如果我们希望查看run方法是不是运行完成或者终止run方法的进行都是不能做到了，为了解决这个问题java5以后新增了callable和future接口，callable接口中提供了一个call方法，这个call方法类似于前面提到的run方法，但是它可以有返回值，这个返回值可以被Future接口实现类获取，call方法可以声明和抛出异常。

下面提供了一段使用这两个接口的实例：

~~~java
public class CallableTest2 {
	public static void main(String [] args){
		//声明对象
		MyCallable m1=new MyCallable(1000);
		MyCallable m2=new MyCallable(2000);
		//将cllable对象封装到FutureTask对象
		FutureTask<String> ft1=new FutureTask<String>(m1);
		FutureTask<String> ft2=new FutureTask<String>(m2);
		//创建线程池
		ExecutorService executor=Executors.newFixedThreadPool(2);
		executor.execute(ft1);
		executor.execute(ft2);
		while(true){
			try{
				if(ft1.isDone() && ft2.isDone()){
					System.out.println("done");
					executor.shutdown();
					return;
				}
				// 任务1没有完成，会等待，直到任务完成 
				if(!ft1.isDone()){
					System.out.println("future1 output"+ft1.get());
				}
				System.out.println("wait future2");
				//这里应该就是规定查询的时间
				String s=ft2.get(200L,TimeUnit.MILLISECONDS);
				if(s!=null){
					System.out.println("future2 output"+s);
				}
			}catch(Exception e){
				
			}
			}	
	}
}
class MyCallable implements Callable<String>{
	private long waitTime;
	public MyCallable(int time){
		this.waitTime=time;
	}
	@Override
	public String call() throws Exception {
		Thread.sleep(waitTime);
		return Thread.currentThread().getName();
	}
	
}

~~~

Future接口中的方法

1.boolean cancel（）：取消任务执行，其中参数指定是否立即中断任务执行或者等待任务结束

2.boolean isCancelled（）：确认任务是否已经取消，任务正常完成前将其取消则返回true

3.boolean isDone（）：确认任务是否已经完成，任务正常终止、异常或取消都将返回true

4.V get ：该方法等待任务执行结束然后获得V类型的结果

5.V get(long timeout,TimeUnit unit):参数timeout指定超时时间，unit指定时间的单位

其实Future接口的工作主要有三个：1.得到返回值  2.判断线程是不是结束  3.终止线程

1.3 线程的生命周期

线程的生命周期：创建状态new 可运行状态 runnable 阻塞状态blocked 等待状态Time  计时状态Time Waiting 终止状态

1.4 线程调度

 线程睡眠：需要让当前正在执行的线程暂停一段时间，则通过Thread类的静态方法sleep使其进入等待状态
         	（注意：sleep是Thread的方法，而notify是Object的方法）
 线程让步：调用yield方法可以实现进程让步，它与sleep类似，也会暂停正在执行的线程，让当前线程交出CPU权限，单yoeld方法只能让拥有相同优先级或优先级更高的线程有获取CPU执行的机会。如果可运行线程队列中的线程的优先级都没有当前线程的优先级高，则当前线程会继续执行
yield是Thread类的方法
线程协作：
若需要一个线程运行到某一个点时，等待另一个线程运行结束后才能继续进行，这种情况可以通过join方法来实现。
在A线程中，需要等到B线程运行结束后再继续A线程，写法是在A线程中写上B.join（）

~~~java
public class joinTest {
	public static void main(String [] args){
		subThread st=new subThread();
		st.start();
		for(int i=0;i<10;i++){
			System.out.println("我是主线程");
			if(i==5){
				try {
					st.join();
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}	
			}
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}

}
class subThread extends Thread{
	public void run(){
		for(int i=1;i<=10;i++){
			System.out.println("我是子线程");
		}
		try {
			sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}

~~~



1.5 守护线程

线程可以分为两类一类是守护线程，一类是用户线程。

守护线程主要负责一些后台的维护，例如jvm的垃圾回收线程

守护线程依赖于创建他的线程而用户线程不依赖，就是说守护线程会随着创建他的线程的结束而结束而用户线程一直会运行到自己结束

可以通过setDaemon来设置一个线程是用户线程还是守护线程，但是这个修改必须在线程start以前修改。

1.6 线程同步

这个是线程问题的一个难点，在进行多线程程序设计时，有时需要多个线程共享一段代码，从而实现共享同一个私有成员或类的静态成员的目的，如果不对线程进行控制，就很容易造成线程无序地访问这些共享资源，而导致无法得到正确的结果，这些问题通常成为线程安全问题。

下面的例子就是一个线程安全的问题：

~~~java
public class ThreadUnsafe {
	public static void main(String [] args){
		ShareData sd=new ShareData();
		for(int i=0;i<10;i++){
			new Thread(sd).start();
		}	
	}
}
class ShareData implements Runnable{
	public int num=0;
	private void add(){
		int temp;
		for(int i=0;i<1000;i++){
			temp=num;
			temp++;
			num=temp;
		}
		System.out.println(Thread.currentThread().getName()+"-"+num);
		
	}
	public void run(){
		add();
	}
	
}

~~~

我们本来的期望是，用10个线程每个线程将num的值增加1000,10个线程就加到了10000.但是运行结果并不是我们想要的结果，问题就处在各个线程对num的调用的无序性，为了解决这个问题java为我们提供了锁机制，使用synchronized修饰代码块或者方法，被修饰的代码块一旦被一个线程获取。另外一个线程就不能使用，我们可以将上文中的add方法加锁

~~~java
private synchronized void add(){
		int temp;
		for(int i=0;i<1000;i++){
			temp=num;
			temp++;
			num=temp;
		}
		System.out.println(Thread.currentThread().getName()+"-"+num);
		
	}
~~~



但是synchronized不是完美的，试想一下如果有两个对象A和B，如果不恰当的使用sychronized关键字管理线程对特定对象的访问，被允许执行的线程拥有对变量或对象的排他性访问权，线程1先访问A后访问B，线程2先访问B后访问A，1得不到B就不放A，2得不到A就不放B这就僵持住了

线程间的通信

java 控制线程间通信的方法有三个：wait（） notify（）notifyAll（） 他们都是Object类的final类

wait（）：让出资源，线程从可运行状态进入到等待状态，这里要注意和sleep的区别，sleep不会让出资源

notify():可以唤醒等待队列中的第一个等待同一个共享资源的线程

notifyAll：唤醒所有正在等待同一个资源的线程

# 第2节  日期类

> Date类 Calendar类 DateFormat类 SimpleDateFormat类

## 2.1 Date类

Date类是较早处理时间对象的类，现在其大部分功能已经被Calendar取代，下面是这个类的几个比较常用的方法

boolean after（Date when）:测试此日期是否在指定日期之后

boelan before（Date when）:测试此日期是否在指定日期之前

Object clone（）：返回此对象副本

int compareTo（Date anotherDate）：比较两个日期的顺序如果参数Date==此Date则返回0

​											参数Date在此Date之前返回值小于0

​											参数Date在此Date之后返回值大于0

boolean equals 比较两个日期的相等性

long getTime：返回从1970年1月1日 00:00:00 依赖的Date对象表示的毫秒数

void setTime（long time）

Strng toString（）把此对象转换为字符串形式

下面我们使用一个类来练习这些方法的使用

```java
import java.util.Date;

public class DateText {
	public static void main(String [] args){
		//声明一个类对象
		Date date1=new Date();
		//克隆对象
		Date date2=(Date)date1.clone();
		//设置date2对象
		date2.setTime(1555328566);
		//比较时间的前后
		System.out.println(date1.after(date2));
		System.out.println(date1.before(date2));
		//得到时间
		System.out.println(date2.getTime());
		String s=date1.toString();
		System.out.println(s);
		
	}
}
```

## 2.2  Calendar

在jdk1.1以后就推荐使用Calendar类，这个类要比Date类强大很多

Calendar类是一个抽象类类（这里要联想到一个知识点就是抽象类和接口一样都不能实例化）

Calendar没有公共的构造方法，得到Calender类的写法是：

'Calendar c=Calendar.getInstrance()'

------

这里跑题一下，我们来想一个问题，既然我们知道Calendar类是一个抽象类，他又号称给我们提供了一个类似于懒汉模式的声明对象的方法，可是不管用什么模式他都不能实例化呀，那就让我们来看看源码

首先我们要看getInstrance的源码：

```java
public static Calendar getInstance(){ 
    return createCalendar(TimeZone.getDefault(),Locale.getDefault(Locale.Category.FORMAT)); 
} 
public static Calendar getInstance(TimeZone zone){ 
      return createCalendar(zone,Locale.getDefault(Locale.Category.FORMAT)); 
  } 
public static Calendar getInstance(Locale aLocale){ 
    return createCalendar(TimeZone.getDefault(),aLocale); 
} 
public static Calendar getInstance(TimeZone zone,Locale aLocale){ 
    return createCalendar(zone,aLocale); 
}
```

这个方法有四个重载，大概都是需要提供时区和地区的参数，这个我们先不管，因为即使我们不写JVM也会把我们用的电脑里面的信息告诉他。这四个重载方法把我们的线索指到了createCalendar这个方法，我们接下来看看这个方法的源码

```java
private static Calendar createCalendar(TimeZone zone, Locale aLocale){ 
CalendarProvider provider = LocaleProviderAdapter .getAdapter(CalendarProvider.class, aLocale) .getCalendarProvider(); 
if (provider != null) { 
try { return provider.getInstance(zone, aLocale); 
} catch (IllegalArgumentException iae) 
{ // fall back to the default instantiation } 
} 
Calendar cal = null; 
if (aLocale.hasExtensions()) {
String caltype = aLocale.getUnicodeLocaleType("ca"); 
if (caltype != null) { 
switch (caltype) {
case "buddhist": cal = new BuddhistCalendar(zone, aLocale); 
break; case "japanese": cal = new JapaneseImperialCalendar(zone, aLocale); 
break; case "gregory": cal = new GregorianCalendar(zone, aLocale); break; } 
} 
} if (cal == null) { 
// If no known calendar type is explicitly specified, 
// perform the traditional way to create a Calendar: 
// create a BuddhistCalendar for th_TH locale, 
// a JapaneseImperialCalendar for ja_JP_JP locale, or 
// a GregorianCalendar for any other locales.
// NOTE: The language, 
 //前面的磨磨唧唧不用管其实还是在讨论时区地区巴拉巴拉的，我们可以看new后面的东西
  
country and variant strings are interned. if (aLocale.getLanguage() == "th" && aLocale.getCountry() == "TH") { cal = new BuddhistCalendar(zone, aLocale);
} else if (aLocale.getVariant() == "JP" && aLocale.getLanguage() == "ja" && aLocale.getCountry() == "JP") { 
    cal = new JapaneseImperialCalendar(zone, aLocale); }
else { 
    cal = new GregorianCalendar(zone, aLocale); } 
} 
    return cal;
}

```



通过对new后面的代码的分析，我们终于找到了问题的答案，其实他是找了好多不同的子类apaneseImperialCalendar、BuddhistCalendar、GregorianCalendar来新建的对象

跑题结束

------

### 使用Calendar类代表指定的时间

public final void set(int year,int mouth,int date)

这里要注意语法值为实际的月份减去1，比如我们要设定3月 这里就输入个2就行

public void set（int field，int value）

field为要设置字段的类型，常见的类型为：

Calendar.TEAR  年份

Calendar.MONTH 月份

Calendar.DATE 日期

Calendar.DAY_OF_MONTH 日期 与上面的完全相同

Calendar.HOUR 12小时的小时数

Calendar.HOUR_OF_DAY 24小时制的小时数

Calendar.MINUTE 分钟

Calendar.SECOND 秒

Calendar.DAY_OF_WEEK 星期几

### 获得Calendar类中的信息

就是使用get方法

int year=calendar.get(Calendar.YEAR)

别的参数类似

要注意周日是1 周一是2 以此类推

### add方法

public  abstract void add(int field,int amout)

### after方法

public boolean after(Object when)

### getTime()

public final Date getTime()

这个方法是将Calendar类型的对象转换为对应的Date类对象

### setTime（）

public final void setTime(Date date)

将Date对象转换为Calendar对象

### Calendar对象和相对时间的转换

getTimeInMills（）

setTimeInMills（）

下面用一个例子来看看Canlendar的使用

```java
import java.util.Calendar;
import java.util.Date;

public class CalendarTest {
public static void main(String [] args){
	//得到对象
	Calendar c1=Calendar.getInstance();
	Calendar c2=Calendar.getInstance();
	//设置时间
	c2.set(Calendar.YEAR, 2018);
	//得到时间
	System.out.println(c2.get(Calendar.YEAR));
	//和日期的相互转换
	Date date=c1.getTime();
	Calendar c3=Calendar.getInstance();
	c3.setTime(date);
	//和秒数的相互转换
	long l1=c3.getTimeInMillis();
	c3.setTimeInMillis(l1);
	
	
}
}
```

## 2.3 DateFormat类

对于时间我们需要多种个性化的格式，为了避免时间格式的单一性java为我们提供了DateFormat类来方便我们对时间的格式个性化设置。

DateFormat是一个抽象类，实例化这个类的时候不能用new而是通过工厂类，这个的具体实现方法应该跟我们前文中分析的一样。

DateFormat df=DateFormat.getDateInstrance

### format和parse

format意思为格式化

parse意思为解析

通过字面意思就可以想象的到format是将Date对象转换为一个字符串，即对时间的格式化输出

parse方法是将字符串解析为Date对象

具体的解析和转换格式在声明DateFormat对象时可以写入或者使用applyPattern方法

## SimpleDateFormat类

SimpleDateFormat类是DateFormat很常用的子类

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateFormat01 {
public static void main(String [] args){
	SimpleDateFormat sdf=new SimpleDateFormat();
	sdf.applyPattern("yyyy-dd-MM");
	Date date=new Date();
	System.out.println(date);
	String s=sdf.format(date);
	System.out.println(s);
	Date date2;
	try {
		date2 = sdf.parse("2019-25-3");
		System.out.println(date2);
	} catch (ParseException e) {
		// TODO Auto-generated catch block
		e.printStackTrace();
	}	
}
}
```



































​		

