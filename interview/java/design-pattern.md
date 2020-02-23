---
title: 设计模式
permalink: /interview/java/design-pattern
key: design-pattern
---

### 【23种设计模式】

- 创建型模式，共五种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。
- 结构型模式，共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。
- 行为型模式，共十一种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。
  



### 【你了解哪些常用的设计模式】

- ​	单例模式

- ​	策略模式
- ​	代理模式
- ​	观察者模式
- ​	装饰模式
- ​	适配器模式
- ​	简单工厂模式
- ​	建造者模式



### 【单例模式的几种实现方式】

一、饿汉式

~~~java
/**
 * 饿汉式单例模式，可以保证多个线程下的唯一实例，getInstance方法性能较高，但是无法进行懒加载
 * 缺点：类加载的时候单例对象就产生了，如果类成员占有的资源比较多，这种方法较为不妥。
 */
public final class HungerSingleton {
    private static HungerSingleton instance = new HungerSingleton();
    
    //私有构造函数不允许外部new
    private HungerSingleton(){}
    
    public static HungerSingleton getInstance(){
        return instance;
    }
}
~~~

二、懒汉式

~~~java
/**
 * 懒加载单例模式
 * 缺点：多线程环境下不能保证单例的唯一性
 */
public final class LazySingleton {
    
    //定义实例但是不直接初始化
    private static LazySingleton instance = null;
    
    //私有构造函数不允许外部new
    private LazySingleton(){}
    
    public static LazySingleton getInstance(){
        //多线程环境下多个线程同时到达这一步，且instance=null时将会创建多个实例
        if(null==instance)
            instance = new LazySingleton();
        return instance;
    }
}
~~~



三、双重校验锁

~~~java
/**
 * 双重校验单例模式
 */
public final class DoubleCheckedSingleton {
    
    //定义实例但是不直接初始化,volatile禁止重排序操作，避免空指针异常
    private static volatile DoubleCheckedSingleton instance = null;
    
    //私有构造函数不允许外部new
    private DoubleCheckedSingleton(){}
    
    public static DoubleCheckedSingleton getInstance(){
        if(null==instance){
            synchronized (DoubleCheckedSingleton.class){
                if (null==instance) {
                    instance = new DoubleCheckedSingleton();
                }
            }
        }
        return instance;
    }
}
~~~



四、静态内部类

~~~java
/**
 * 静态内部类实现单例模式，通过类加载机制保证了单例对象的唯一性（静态内部类只会加载一次）
 */
public final class HolderSingleton {
    private HolderSingleton(){}
    
    private static class Holder{
        private static HolderSingleton instance = new HolderSingleton();
    }
    
    public static HolderSingleton getInstance(){
        return Holder.instance;
    }
}
~~~



五、枚举类

~~~java
/**
 * 单例模式的枚举写法《Effective Java》作者力推的方式，jdk1.5以上才适用
 */
public enum  EnumSingleton {
    INSTANCE;
    
    public void method(){
        //do something
    }
    
    public static EnumSingleton getInstance(){
        return INSTANCE;
    }

}
~~~



### 【装饰者模式讲讲】    

