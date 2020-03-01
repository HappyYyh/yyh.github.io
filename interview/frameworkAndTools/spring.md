---
title: Spring
permalink: /interview/frameworkAndTools/spring
key: frameworkAndTools-spring
---

## Bean

### 【讲一讲对Spring的理解】

Spring是一个轻量级控制反转(IoC)和面向切面(AOP)的**容器**框架。



### 【讲一下spring的作用是什么】

1. spring的**轻量级**是是从它的大小和开销来说的，完整的Spring框架可以在一个大小只有1MB多的JAR文件里发布。并且Spring所需的处理开销也是微不足道的。

2. Spring是**非侵入式**的,spring的api是不会出现在业务逻辑上出现的，对于应用而言，业务逻辑可以从当前应用剥离出来，实现复用，对于框架而言，业务逻辑也可以从spring框架中快速的移植到别的框架
3. spring**提供容器功能**，容器可以管理对象的生命周期，对象和对象之间的依赖关系等。通常我们都是可以写一个配置文件，在上面定义对象的名字等，在容器启动以后，这些对象就被实例化好了，我们可以直接去用。而且依赖关系也建立好了。

4. spring的**ioc**指的是控制权的转移，将控制权交给容器，调用者可以专心自己的业务逻辑就可以了。对象控制权由调用者移交给容器，使得调用者不必关心对象的创建和管理，专注于业务逻辑开发；解耦对象间的依赖关系，避免通过硬编码的方式耦合在一起；

5. spring的**aop**是面向切面编程，一种新的模块化方式，专门处理系统各模块中的交叉关注点问题，将具有横切性质的系统级业务提取到切面中，与核心业务逻辑分离(解耦)；

6. spring可以很好的和别的**框架组合**



### 【spring bean是什么】

官方文档对于bean的解释：

> In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.

翻译过来就是：

> 在 Spring 中，构成应用程序**主干**并由**Spring IoC容器**管理的**对象**称为**bean**。bean是一个由Spring IoC容器实例化、组装和管理的对象。

所以总结

1. bean是对象，一个或者多个不限定
2. bean由Spring中一个叫IoC的东西管理
3. 我们的应用程序由一个个bean构成



### 【Spring Bean的生命周期】

​	Spring Bean的完整生命周期从创建Spring容器开始，直到最终Spring容器销毁Bean，这其中包含了一系列关键点：

- ① 通过构造器或工厂方法创建bean实例
- ② 为bean的属性设置值和对其他bean的引用
- ③ 调用bean的初始化方法
- ④ bean可以使用了
- ⑤ 当容器关闭时，调用bean的销毁方法



### 【Bean的作用域，几种scope区别】

1. **singleton：默认的作用域，仅为每个Bean对象创建一个实例**
   把一个bean定义设置为singlton作用域时，Spring IoC容器只会创建该bean定义的唯一实例。这个单一实例会被存储到单例缓存（singleton cache）中，并且所有针对该bean的后续请求和引用都将返回被缓存的对象实例。

   

2. **prototype：可以根据需要为每个Bean对象创建多个实例**
   Prototype作用域的bean会导致在每次对该bean请求（将其注入到另一个bean中，或者以程序的方式调用容器的getBean()方法）时都会创建一个新的bean实例。根据经验，对所有有状态的bean应该使用prototype作用域，而对无状态的bean则应该使用singleton作用域。
   请注意，典型情况下，DAO不会被配置成prototype，因为一个典型的DAO不会持有任何会话状态，因此应该使用singleton作用域。

   

3. **web作用域**
   - **request**：为每个HTTP请求创建它自有的一个Bean实例，仅在Web相关的ApplicationContext中生效。
   - **session**：为每个HTTP会话创建一个实例，仅在Web相关的ApplicationContext中生效。
   - **global session**：为每个全局的HTTP会话创建一个实例。一般仅在porlet上下文中用生效。同时仅在Web相关的ApplicationContext中生效。



### 【Spring BeanDefinition作用】  

**类关系：** 

BeanDefinition接口继承自BeanMetadataElement和AttributeAccessor两个接口。

- `BeanMetadataElement`：bean元数据，返回该bean的来源。
- `AttributeAccessor`：Spring定义的属性访问器，对Bean的属性进行操作的API,例如设置属性、获取属性、判断是否存在该属性，返回bean所有的属性名称等。

**属性列表：**

- `String SCOPE_SINGLETON`，bean的作用范围，单例模式
- `String SCOPE_PROTOTYPE`，bean的作用范围为prototype，在Spring生命周期中，会存在多个，由垃圾回收期管理其生命周期。
- `int ROLE_APPLICATION`：bean的角色定义，默认，为应用程序定义。
- `int ROLE_SUPPORT`：bean的角色定义，为应用程序定义的比较大的对象。
- `int ROLE_INFRASTRUCTURE`：Spring内部定义的Bean对象。

**核心方法详解：**

- `void setBeanClassName(String beanClassName)` 

  该Bean的class name。

- `void setScope(String scope)`

  bean的生命周期，单例还是prototype。

- `void setLazyInit(boolean lazyInit)`

  lazyInit，是否延迟加载，如果设置为true,在需要用到时再初始化。

- `void setDependsOn(String… dependsOn)`

  dependsOn一般用于两个bean之间没有显示依赖，但后一个Bean需要用到前一个Bean执行初始方法后的结果。例如在< bean id=”a” dependsOn=”b”/> 时，在初始化a时首先先初始化b，在销毁b之前会先销毁a。

- `void setAutowireCandidate(boolean autowireCandidate)`

  设置该对象是否可以被其他对象自动装配。

  spring通过配置bean的autowire属性设置自动装配方式：

    - no：不使用自动装配，必须通过ref元素指定依赖，为autowire默认值。

    - byName：使用属性名自动装配，如果存在一个与指定属性名相同类型的bean则自动装配，如果有多个，则抛出异常。

    - byType：根据类型自动状态，如果存在与指定属性类型相同的bean,则自动装配，如果有多个，则抛出异常。

    - constructor：与byType类似，不同之处在于它使用的是构造器的参数类型。

    - autodetect：通过bean的自省机制来决定是使用constructor还是byType来进行自动装配。如果有默认构造
  器，则使用byType，否则使用constructor。

- `void setPrimary(boolean primary)`

  如果其他对象按照类型自动装配时发现有多个符合类型的多个实现bean，如果bean的primary属性为true，则以primary为true的优先，当然如果有多个primary为true，则抛出异常。

- `void setFactoryBeanName(String factoryBeanName)`

  设置bean的factoryBeanName。

- `void setFactoryMethodName(String factoryMethodName)`

  设置bean工厂的方法名，Spring在实例化Bean对象时支持工厂方法设计模式，在初始化bean时不是通过bean的class发射创建 bean实例，而是根据factoryBeanName反射出工厂的实例，然后调用它的实例方法factoryMethodName来创建bean实例。

- `ConstructorArgumentValues getConstructorArgumentValues()`

  获取bean的构造方法参数。

- `MutablePropertyValues getPropertyValues()`

  获取实例bean的所有属性。

- `boolean isSingleton()`

  是否是单例。

- `boolean isPrototype()`

  是否是非单例。

- `boolean isAbstract()`

  是否是抽象的。



### 【beanFactory和factoryBean的区别】

1. BeanFactory 以Factory结尾，表示它是一个工厂类，用于管理Bean的一个工厂;在Spring中，所有的Bean都是由BeanFactory(也就是IOC容器)来进行管理的。
2. 但对FactoryBean而言，这个Bean不是简单的Bean，而是一个能生产或者修饰对象生成的工厂Bean,它的实现与设计模式中的工厂模式和修饰器模式类似。



### 【说一下Spring容器的启动过程】 

spring容器的启动方式有两种：

1. **自己提供ApplicationContext自己创建Spring容器**

   ```java
   ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("spring.xml")
   User user = (User) context.getBean()}
   ```

   当通过ClassPathApplicationContext初始化容器时，它会根据定位加载spring.xml配置，然后解析配置文件，生成Bean，注册Bean，最后我们在通过getBean获取对象，这一现象就跟IOC容器的初始化过程一样，资源定位、资源加载、资源解析、生成Bean、Bean注册

2. **Web项目中在web.xml中配置监听启动**

   org.springframework.web.context.ContextLoaderListener

   在web.xml文件中进行配置，根据web容器的启动而启动

   ```xml
   <listener>
       <listener-class>
       	org.springframework.web.context.ContextLoaderListener
       </listener-class>
   </listener>
   ```

   ContextLoaderListener实现了ServletContextListener接口，实现了两个方法

   ```java
   //初始化WebApplicationContext容器
   public void contextInitialized(ServletContextEvent event) {
       this.initWebApplicationContext(event.getServletContext());
   }
   
   //销毁WebApplicationContext容器
   public void contextDestroyed(ServletContextEvent event) {
       this.closeWebApplicationContext(event.getServletContext());
       ContextCleanupListener.cleanupAttributes(event.getServletContext());
   }
   ```

   参数ServletContextEvent能直接获取servletContext也就是java web容器的上下文

   在父类中调用了intitWebApplicationContext方法，传入了ServletContext,在父类方法中对ServletContext进行了判断，检测servletContext的属性中是否存在spring的根上下文属性，如果存在则是错误的,因为表明已经有一个根上下文已经启动,再启动会冲突,所以避免重复启动!



## IOC

### 【谈谈你对SpringIOC和DI的理解】

**IOC概念:**

> Ioc—Inversion of Control，即**“控制反转”**，不是什么技术，而是一种**设计思想**。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。

**IOC作用：**

有了IoC容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。

**DI概念：**

> DI—Dependency Injection，即“依赖注入”：**组件之间依赖关系**由容器在运行期决定，形象的说，即**由容器动态的将某个依赖关系注入到组件之中**。

**DI作用：**

通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

**IoC和D是同一个概念的不同角度描述**



### 【Spring IOC容器的启动过程】

几乎等同于上题《说一下Spring容器的启动过程》

Spring 的启动流程主要是**定位 -> 加载 -> 注册 -> 实例化**

- 定位 - 获取配置文件路径
- 加载 - 把配置文件读取成 BeanDefinition
- 注册 - 存储 BeanDefinition
- 实例化 - 根据 BeanDefinition 创建实例



### 【Spring @Autowired (@Resource类似）怎么做的】

Autowired源码

~~~java
@Target({ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.PARAMETER, ElementType.FIELD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Autowired {
    boolean required() default true;
}
~~~

其实现逻辑位于类:`AutowiredAnnotationBeanPostProcessor`之中,其核心代码如下：

~~~java
private InjectionMetadata buildAutowiringMetadata(final Class<?> clazz) {
    LinkedList<InjectionMetadata.InjectedElement> elements = new LinkedList<>();
    Class<?> targetClass = clazz;//需要处理的目标类

    do {
        final LinkedList<InjectionMetadata.InjectedElement> currElements = new 					LinkedList<>();

        /*通过反射获取该类所有的字段，并遍历每一个字段，并通过方法findAutowiredAnnotation遍历每一个字段的所用注解，并如果用autowired修饰了，则返回auotowired相关属性*/  

        ReflectionUtils.doWithLocalFields(targetClass, field -> {
            AnnotationAttributes ann = findAutowiredAnnotation(field);
            if (ann != null) {//校验autowired注解是否用在了static方法上
                if (Modifier.isStatic(field.getModifiers())) {
                    if (logger.isWarnEnabled()) {
                        logger.warn("Autowired annotation is not supported on static 							fields: " + field);
                    }
                    return;
                }//判断是否指定了required
                boolean required = determineRequiredStatus(ann);
                currElements.add(new AutowiredFieldElement(field, required));
            }
        });
        //和上面一样的逻辑，但是是通过反射处理类的method
        ReflectionUtils.doWithLocalMethods(targetClass, method -> {
            Method bridgedMethod = BridgeMethodResolver.findBridgedMethod(method);
            if (!BridgeMethodResolver.isVisibilityBridgeMethodPair(method, 								bridgedMethod)) {
                return;
            }
            AnnotationAttributes ann = findAutowiredAnnotation(bridgedMethod);
            if (ann != null && method.equals(ClassUtils.getMostSpecificMethod(method, 					clazz))) {
                if (Modifier.isStatic(method.getModifiers())) {
                    if (logger.isWarnEnabled()) {
                        logger.warn("Autowired annotation is not supported on static 							methods: " + method);
                    }
                    return;
                }
                if (method.getParameterCount() == 0) {
                    if (logger.isWarnEnabled()) {
                        logger.warn("Autowired annotation should only be used on methods 							with parameters: " + method);
                    }
                }
                boolean required = determineRequiredStatus(ann);
                PropertyDescriptor pd = BeanUtils.findPropertyForMethod(bridgedMethod, 					clazz);
                currElements.add(new AutowiredMethodElement(method, required, pd));
            }
        });
        //用@Autowired修饰的注解可能不止一个，因此都加在currElements这个容器里面，一起处理		
        elements.addAll(0, currElements);
        targetClass = targetClass.getSuperclass();
    }
    while (targetClass != null && targetClass != Object.class);

    return new InjectionMetadata(clazz, elements);
}
~~~

方法返回的就是包含所有带有autowire注解修饰的一个`InjectionMetadata`集合

~~~java
public InjectionMetadata(Class<?> targetClass, Collection<InjectedElement> elements) {
    this.targetClass = targetClass;
    this.injectedElements = elements;
}
~~~

获取到目标类以后，就可以实现autowired的依赖注入逻辑了：

~~~java
@Override
public PropertyValues postProcessPropertyValues(
    PropertyValues pvs, PropertyDescriptor[] pds, Object bean, String beanName) throws BeanCreationException {

    InjectionMetadata metadata = findAutowiringMetadata(beanName, bean.getClass(), pvs);
    try {
        metadata.inject(bean, beanName, pvs);
    }catch (BeanCreationException ex) {
        throw ex;
    }catch (Throwable ex) {
        throw new BeanCreationException(beanName, "Injection of autowired dependencies failed", ex);
    }
    return pvs;
}

/**inject也使用了反射技术并且依然是分成字段和方法去处理的。
 * Either this or {@link #getResourceToInject} needs to be overridden.
 */
protected void inject(Object target, @Nullable String requestingBeanName, @Nullable 		PropertyValues pvs)
    throws Throwable {

    if (this.isField) {
        Field field = (Field) this.member;
        ReflectionUtils.makeAccessible(field);
        field.set(target, getResourceToInject(target, requestingBeanName));
    }else {
        if (checkPropertySkipping(pvs)) {
            return;
        }
        try {
            Method method = (Method) this.member;
            ReflectionUtils.makeAccessible(method);
            method.invoke(target, getResourceToInject(target, requestingBeanName));
        }
        catch (InvocationTargetException ex) {
            throw ex.getTargetException();
        }
    }
}
~~~



  

### 【Spring如何解决循环依赖】 

**循环依赖概念：**

> 循环依赖-->循环引用。--->即2个或以上bean 互相持有对方，最终形成闭环。
>
>   eg：A依赖B，B依赖C，C又依赖A

**如何解决：**

后续查看：https://zhuanlan.zhihu.com/p/84267654

- Spring是通过递归的方式获取目标bean及其所依赖的bean的；
- Spring实例化一个bean的时候，是分两步进行的，首先实例化目标bean，然后为其注入属性。

​    结合这两点，也就是说，Spring在实例化一个bean的时候，是首先递归的实例化其所依赖的所有bean，直到某个bean没有依赖其他bean，此时就会将该实例返回，然后反递归的将获取到的bean设置为各个上层bean的属性的。



### 【IOC底层实现】 

- **①使用XML文件配置**
- **②dom4j解析xml**
- **③工厂设计模式**
- **④反射机制创建对象**



### 【如何自己设计IOC框架】   



## AOP

### 【Spring的AOP理解】

### 【什么是静态代理和动态代理？它们的优缺点】  

### 【动态代理有几种实现方式】  

### 【JDK动态代理和CGLIB动态代理的区别】  

哪种情况下用JDK动态代理，哪种情况下用CGLIB动态代理？   

### 【AOP的底层实现原理吗】   



## SpringMVC

### 【SpringMVC处理流程】

  

### 【SpringMVC不同用户登录的信息怎么保证线程安全的】  



## SpringBoot

### 【对SpringBoot的理解】


  

### 【SpringBoot的自动配置】   

### 【SpringBoot注解和原理】 

 

### 【SpringBoot定时任务 】

### 【SpringMVC、Spring和SpringBoot的区别】  