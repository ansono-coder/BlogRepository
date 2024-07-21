---
title : 使用InvocationHandler,Method,proxy完成动态代理
categories: 编程技术
tag : 
    - java 
    - 动态代理
---
## 为什么要用代理模式？
- 通过代理模式，我们可以调用原先不好调用的方法，比如在MVC架构模式中，业务层中开启事务若不用代理模式，就会和持久化层耦合，用代理模式就可以很好的解耦和
- 对目标类中原先存在的方法进行修改，比如，目标类是其他开发者所写，我们没有修改权限，这个时候，就可以通过代理模式来修改对方类中的方法
## 静态代理和动态代理
### 静态代理：
>通过创建代理类文件来对目标类进行访问或者修改，在代理类中直接调用目标类的方法，或者修改目标类中的方法，main方法调用时，直接创建代理类，调用代理类中相应的方法完成对目标类的调用
>
- 优点： 可以实现对目标类中方法的修改
- 缺点： 随着代理类数量变多，类文件数量会非常多且修改类文件次数也会增多，这不利于开发的进行
### 动态代理：
>通过jdk自带的反射机制或者一个名叫**cglib**的开源项目完成动态代理，也就是在项目运行期间完成代理类的创建和目标类中方法的调用
>
- 优点：项目运行期间完成，减少了修改类文件的次数，类文件数量较少
- 缺点：未知，因为我也没有深入
## 本文的重点是利用jdk的反射机制完成动态代理（前提：方法一定存在接口中）
### 三个主要的类（InvocationHandler,Method,proxy）
- **InvocationHandler**
```java
//重点是此类中的invoke()
/*
    这个方法是动态代理的代理类方法，是整个动态代理中的核心方法
    @param proxy 代理对象，jdk自己决定
    @param method 具体执行的方法，无需我们关心
    @param args 参数列表 jdk自己决定
**/
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    //target 是目标类对象，我们需要传递这个对象到此类中
    method.invoke(target,args) ;
}
```
- Method
```java
//Method 类中最主要的方法就是这个invoke()
/*
    这个方法主要用来执行传入对象的对应的方法
    @param obj 对象的引用
    @param args 对象具体方法的形式参数类型，如xx.class类型
**/
public Object invoke(Object obj,Object... args)throws IllegalAccessException,IllegalArgumentException,InvocationTargetException {

}

```
- proxy
```java
//这个类中最主要的方法是newProxyInstance
/*
    这个方法用来创建代理类对象，返回值要强转为方法所在的接口
**/
public static Object newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)throws IllegalArgumentException {

}
```
### 具体实现的步骤（以sell（）方法为例）
- 创建方法接口
```java
public interface Functions {

    public float sell() ;
}
```
- 创建实现类
```java
public class sellImpl implements Functions {

    @override
    public float sell() {

        return 20.0f ;
    }
}
```
- 实现InvocationHandler接口
```java
public class InvocationHandlerImpl implements InvocationHandler {

    public Object target = null ;

    public invocationHandlerImpl(Object target) {
        this.target = target ;
    }
    @override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        float result = 0f ;
        Object res = Method.invoike(target,args) ;
        if(res != null)
        {
            result = (float)res ;
        }
        return result ;

    }

}
```
- 在main方法中调用proxy的静态方法newProxyInstance
```java
public class Main {

    public static void main(String[] args) {
        Functions target = new SellImpl() ;
        InvocationHandler inImpl = new InvocationHandlerImpl() ;
        Functions fun = (Functions)proxy.newProxyInstance(target.getClass().getClassLoader(),target.getInterfaces(),inImpl) ;
        float result = fun.sell() ;
        System.out.println("result = " + result) ;

    }

}
```
**以上代码纯手打，如果出现变量名错误，还请见谅，我想表达的主要是这个流程**

### 到此，本文结束，记录完成


