# reflection

Java中反射，
- 对于类获取其所有属性、方法；
- 对于对象，可以调用该对象任何方法。

## 使用场景
编译时，
运行时，

```java
Person p=new Student(); // 编译时 Person; 运行时 Student
```

外部传入对象 编译时 Object，运行时 程序需要知道并调用这个对象的方法
运行时就需要通过反射获取相关信息，并执行

## Java工具

反射API用来生成JVM中的类、接口或则对象的信息。

1.Class类：反射的核心类，可以获取类的属性，方法等信息。

2.Field类：Java.lang.reflec包中的类，表示类的成员变量，可以用来获取和设置类之中的属性值。

3.Method类：Java.lang.reflec包中的类，表示类的方法，它可以用来获取类中的方法信息或者执行方法。

4.Constructor类：Java.lang.reflec包中的类，表示类的构造方法。

```java
Class clazz=Class.forName("reflection.Person");

Method[] method=clazz.getDeclaredMethods();
Field[] field=clazz.getDeclaredFields();
Constructor[] constructor=clazz.getDeclaredConstructors();


Person p=(Person) clazz.newInstance();

```