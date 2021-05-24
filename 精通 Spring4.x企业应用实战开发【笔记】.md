## 第4章 IoC容器

### 4.1 概述

IoC 

Inverse of Control  控制反转

某一接口具体实现类的选择权，从调用类中移出，转交给第三方决定。即Spring容器借由Bean配置里进行控制。

IoC可划分为三种类型:laughing: 

构造函数注入，属性注入，接口注入。

**构造函数注入**

调用类的构造函数，将接口实现类通过构造函数变量传入。

**属性注入**

有选择地通过Setter方法完成调用所需依赖的注入，更加灵活方便。可以在需要使用时注入，而非在创建对象时使用构造方法注入，延时加载。

**接口注入**

将调用类所有毅力注入的方法抽取到一个接口中，调用类通过实现该接口提供相应的注入方法。

------

通过容器完成依赖关系注入 (后文详谈)

### 4.2  反射 类加载器

反射Demo

```java

public class Car {
	private String brand;
	private String color;
	private int maxSpeed;

	public Car(){System.out.println("init car!!");}
	public Car(String brand,String color,int maxSpeed){
		this.brand = brand;
		this.color = color;
		this.maxSpeed = maxSpeed;
	}
	public void introduce() {
       System.out.println("brand:"+brand+";color:"+color+";maxSpeed:"+maxSpeed);
	}
	public String getBrand() {
		return brand;
	}
	public void setBrand(String brand) {
		this.brand = brand;
	}
	public String getColor() {
		return color;
	}
	public void setColor(String color) {
		this.color = color;
	}
	public int getMaxSpeed() {
		return maxSpeed;
	}
	public void setMaxSpeed(int maxSpeed) {
		this.maxSpeed = maxSpeed;
	}
}

```

通过反射，获得Class类对象，根据参数组成获得该类的构造函数对象，根据名字创建该类的方法对象，成员属性对象；创建实例后，通过方法对象和属性对象向实例调用方法/传递参数

```java
public class ReflectTest{
    public static Car initByDefaultConst() throws Throwable {

        ClassLoader loader = Thread.currentThread().getContextClassLoader();
        Class clazz = loader.loadClass("com.smart.reflect.Car");
        //通过类的全限定类名装载Car

        //通过参数得到构造函数对象 并创建实例
        Constructor cons = clazz.getDeclaredConstructor((Class[])null);
        Car car = (Car)cons.newInstance();

        //通过方法名和可变长的参数类型来得到方法对象
        Method setBrand = clazz.getMethod("setBrand",String.class);
        //通过invoke方法来调用方法
        setBrand.invoke(car,"红旗CA72");
        Method setColor = clazz.getMethod("setColor",String.class);
        setColor.invoke(car,"黑色");
        Method setMaxSpeed = clazz.getMethod("setMaxSpeed",int.class);
        setMaxSpeed.invoke(car,200);
        return car;
    }

    public static void main(String[] args) throws Throwable {
        Car car = initByDefaultConst();
        car.introduce();
    }

}
```

#### 类装载器 ClassLoader

类装载器把一个类装入jvm需要经过：

装载：查找和导入Class文件

链接：执行校验、准备和解析步骤，其中解析步骤是可以选择的

 1 校验 

 2 准备

 3 解析

初始化：对类的静态变量、静态代码块执行初始化工作

------

   JVM在运行时会产生3个ClassLoader：根装载器、ExtClassLoader(扩展类装载器)和AppClassLoader(应用类装载器).

==根装载器==并非ClassLoader子类，Java不可见，负责装载JRE核心类库，如rt.jar chrsets.jar等。

==ExtClassLoader==负责装载JRE扩展目录ext中的jar包；ClassLoader子类

==AppClassLoader==负责装载Classpath路径下的类包。ClassLoader子类

继承关系：根 -> ExtClassLoader -> AppClassLoader

```java
public class ClassLoaderTest {

    public static void main(String[] args) {
        ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
        System.out.println("current loader:" +classLoader);
        System.out.println("parent loader:" + classLoader.getParent());
        System.out.println("grandparent:" + classLoader.getParent().getParent() );
    }
}
```

```
current loader:sun.misc.Launcher$AppClassLoader@18b4aac2
parent loader:sun.misc.Launcher$ExtClassLoader@6f94fa3e
grandparent:null
```

根装载器 Java无法获得其句柄，返回null

------

**全盘负责委托机制**

JVM装载类使用全盘负责委托机制，

“全盘负责”是指当一个ClassLoader装载一个类时，除非显式地调用另一个ClassLoader，该类所依赖以及引用的类也由这个ClassLoader载入；

“委托机制”是指，先委托父装载类寻找目标，只有在找不到的情况下才从自己的类路径中查找并装载目标类。

如：java.lang.String永远由根装载器来装载，避免装载同名自定义类。

**ClassLoader重要方法**

-- 就记得 loadClass(String name)和getParent()

#### Java反射机制

3个主要反射类

Constructor :

```
 类的构造函数反射类 通过Class#Constructors()方法获取
getConstructor(Class.. parameterTypes)获取拥有特定入参的构造函数反射对象。
newInstance(Object...initargs) 创建一个对象类的实例
```

Method

```
类方法的反射类。
通过Class#getDeclaredMethods()方法可以获取类的所有方法发射对象数组Method[]
主要方法invoke(Object obj,Object[] args)
getReturnType()
getPrameterTypes()
getExceptionType()
getParameterAnnotations() 获取方法的注解信息 java 5.0中的新方法
```

Field

```
类的成员变量的发射类
通过Class#getDeclaredFields()方法可以获取类的成员变量反射对象数组
Class#getDeclredField(String name)获得某个特定名称的成员变量反射对象
主要方法set(Object obj,Object value) obj是目标对象，设为value的值。
```

举例说明

```java
public class PrivateCarReflect {
    public static void main(String[] args) throws Throwable {
        ClassLoader loader = Thread.currentThread().getContextClassLoader();
        Class clazz = loader.loadClass("com.smart.reflect.PrivateCar");
        PrivateCar pcar = (PrivateCar) clazz.newInstance();
        Field colorField = clazz.getDeclaredField("color");

        colorField.setAccessible(true);//取消Java语言的访问检查
        colorField.set(pcar,"红色");

        Method driveMtd = clazz.getDeclaredMethod("drive",(Class []) null);

        driveMtd.setAccessible(true);
        driveMtd.invoke(pcar,(Object[]) null);
    }
}
```

color变量和drive()方法都是私有的，通过类实例变量无法再外部访问私有变量，调用私有方法，但通过反射机制则可以绕过这个限制。

### 4.3资源访问

Spring设计了Resource接口

主要方法

```
exists()
isOpen()
getURL()
getFile()
getInputStream()
```

有很多子接口，完成不同加载资源的方式。

```
FileSystemResource 以文件系统绝对路径的方式进行访问
ClassPathResource 以类路径的方式进行访问
ServletContextResource 相对于Web应用根目录得方式进行访问
```

```java

public class FileSourceExample {
	
	public static void main(String[] args) {
		try {
//			String filePath = "D:/masterSpring/code/chapter4/src/main/resources/conf/file1.txt";
			String filePath = "E:/projects/精通spring 4.x[源码]/chapter4/src/main/resources/conf/file1.txt";
			WritableResource res1 = new PathResource(filePath);
			Resource res2 = new ClassPathResource("conf/file1.txt");

			OutputStream stream1 = res1.getOutputStream();
			stream1.write("欢迎光临\n小春论坛".getBytes());
			stream1.close();

            InputStream ins1 = res1.getInputStream();
			InputStream ins2 = res2.getInputStream();

			ByteArrayOutputStream baos = new ByteArrayOutputStream();
			int i;
			while((i=ins1.read())!=-1){
				baos.write(i);
			}
			System.out.println(baos.toString());

            System.out.println("res1:"+res1.getFilename());
            System.out.println("res2:"+res2.getFilename());            
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

资源加载

地址前缀

```
"classpath:" 只会子第一个加载的包路径下查找  查多个中的第一个
"classpath*:" 或扫描所有这些jar包及类路径下出现的  查多个
```

模板解析器获得资源

```java
public class PatternResolverTest {
    @Test
    public  void getResources() throws Throwable{
        ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        Resource resources[] = resolver.getResources("classpath*:com/smart/**/*.xml");
        assertNotNull(resources);
        for(Resource resource:resources){
            System.out.println(resource.getDescription());
        }
    }
}
```

`使用Resource操作文件时，如果资源配置文件在项目发布时会被打包到JAR中，那么不能使用Resource#getFile()方法，否则会抛出FileNotFoundException 但可以使用Resource#getInputStream()方法获取`

### 4.4 BeanFactory  和 ApplicationContext

BeanFactory为IoC容器；是Spring框架的基础设施，面向Spring本身。

ApplicationContext为应用上下文，面型使用Spring框架的开发者，几乎所有场合都可以直接使用。



 可以被创建和管理java对象，POJO，被Spring称为Bean;

JavaBean需满足一定规范，如提供默认的不带参的构造函数，不依赖于某一特定容器。

BeanFactory位于类结构树的顶端，最主要的方法就是getBean(String beanName)，该方法从容器中返回特定的Bean。其功能通过其他接口得到不断扩展

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 		 		 xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">


    <bean id="car1" class="com.smart.Car"
          p:brand="红旗CA72"
          p:color="黑色"
          p:maxSpeed="200"/>
</beans>
```





```java
public class BeanFactoryTest {
    @Test
    public void getBean() throws Throwable{
        ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        Resource resource = resolver.getResource("classpath:com/smart/beanfactory/beans.xml");
        System.out.println(resource.getURL());
        System.out.println(resource.getDescription());

        DefaultListableBeanFactory factory = new DefaultListableBeanFactory();
        XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(factory);
        reader.loadBeanDefinitions(resource);

        System.out.println("init beanfactory");

        Car car = factory.getBean("car1",Car.class);
        System.out.println("using");
        car.introduce();
    }
}
```



ApplicationContext的主要实现类是 ClassPathXmlApplicationContext和FileSystemXmlApplicationContext，前者默认从类路径加载配置文件，后者默认从文件系统中装载配置文件。

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("com/smart/context/beans.xml");
```

对于ApplicationContext而言，"com/smart/context/beans.xml"等同于"classpath:com/smart/context/beans.xml"

对于FileSystemXmlApplicationContext而言，"com/smart/context/beans.xml"等同于"file:com/smart/context/beans.xml"

------

BeanFactory和ApplicationContext的区别：BeanFactory在初始化容器时，并未实例化Bean,直到第一次访问某个Bean时才实例化目标Bean；而ApplicationContext则在初始化应用上下文时就实例化所有单实例的Bean.故ApplicationContext初始化时间长，但后续调用没有“第一次惩罚”的问题















