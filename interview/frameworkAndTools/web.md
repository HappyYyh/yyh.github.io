---
title: Web
permalink: /interview/frameworkAndTools/web
key: frameworkAndTools-web
---

### 【Session和Cookie的区别】

- **存放内容**

  session和cookie一般都可以存储用户信息

- **存放位置**

  session存储再服务端，cookie存储在客户端

- **过期时间**

  它们都可以设置过期时间；

  如果session一直活动，session就总不会过期，一般来说session30分钟过期

  如果Cookie没有设置expires属性，那么 cookie 的生命周期只是在当前的会话中

- **安全性**

  由于cookie存储在客户端，同时也有可能禁用cookie，所以cookie是不安全的



### 【多个微服务如何共享Session】

1. **Tomcat的session共享**

   **优点**：不需要额外开发，只需搭建tomcat集群即可
   **缺点**：tomcat 是全局session复制，集群内每个tomcat的session完全同步（也就是任何时候都完全一样的) 在大规模应用的时候，用户过多，集群内tomcat数量过多，session的全局复制会导致集群性能下降， 因此，tomcat的数量不能太多，而且依赖tomcat容器移植性不好(所以不采用)

2. **cookie同步session** 

   如JWT(json web token)

   这种完全把客户的登陆信息保存在客户端的cookie中，每次请求带着cookie中的Token
   **优点**：由于完全舍弃了session 会减轻服务器端的压力
   **缺点**：是把信息暴露在外，就算有加密算法还是存在安全问题。禁止使用cookie的情况下无效。

3. **redis 集中管理session(常用)**

   **优点**：redis为内存数据库，读写效率高，并可在集群环境下做高可用


### 【Servelt的生命周期】

- `init()`

  init 方法被设计成只调用一次。它在第一次创建 Servlet 时被调用，在后续每次用户请求时不再调用。

- `service()`

  service() 方法是执行实际任务的主要方法

  像我们经常继承的`HttpServlet`，重写的`doGet`和`doPost`方法都是`service()`方法的一部分

  ~~~java
  protected void service(HttpServletRequest req, HttpServletResponse resp)
          throws ServletException, IOException {
  
          String method = req.getMethod();
          if (method.equals(METHOD_GET)) {
              long lastModified = getLastModified(req);
              if (lastModified == -1) {
                  doGet(req, resp);
              } else {
                  ...
              }
  		...
          } else if (method.equals(METHOD_POST)) {
              doPost(req, resp);
  
          ...
      }
  ~~~

  

- `destory()`

  destroy() 方法只会被调用一次，在 Servlet 生命周期结束时被调用。



### 【Servlet线程安全吗?如何保证安全?】

​	Servlet不是线程安全的。Servlet是单例模式，但是如果在Serlvet中定义类变量，多线程下就有可能出现线程安全问题。

​	**实例变量不正确的使用**是造成Servlet线程不安全的主要原因，所以只要保证变量安全就行：

1. 实现 SingleThreadModel 接口
2. 同步对共享数据的操作



### 【Servlet全局变量是多个线程都可以访问吗】

是的，看上题



### 【Java web过滤器的生命周期】

Filter的生命周期类似于Servlet的生命周期

- `init()`

  web应用程序启动时，web服务器将创建Filter的实例对象并调用其init方法，完成对象的初始化功能,从而为后续的用户请求作好拦截的准备工作.filter对象只会创建一次，init方法也只会执行一次

- `doFilter()`

  执行过滤方法

- `destory()`

  destroy方法在Filter的生命周期中仅执行一次,在destroy方法中，可以释放过滤器使用的资源



### 【WebSocket】  

> WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。

其主要方法是:

~~~java
public void onOpen();//打开连接
public void onClose();//连接关闭
public void onMessage();//收到消息
public void onError();//发送错误
~~~

SpringBoot里面的使用：

~~~java
@Component
@ServerEndpoint(value = "/webSocket/{id}")
@Slf4j
public class WebSocketServer {

    /**
     *     静态变量，用来记录当前在线连接数。应该把它设计成线程安全的。
     */
    private static AtomicInteger atomicInteger = new AtomicInteger(0);

    /**
     * concurrent包的线程安全Set，用来存放每个客户端对应的MyWebSocket对象。
     * 若要实现服务端与单一客户端通信的话，可以使用Map来存放，其中Key可以为用户标识
     */
    public static final CopyOnWriteArraySet<WebSocketServer> webSocketSet = new CopyOnWriteArraySet<>();

    /**
     * 与某个客户端的连接会话，需要通过它来给客户端发送数据
     */
    private Session session;

    private Integer id;

    /**
     * 连接建立成功调用的方法
     *
     * @param session 可选的参数。session为与某个客户端的连接会话，需要通过它来给客户端发送数据
     */
    @OnOpen
    public void onOpen( @PathParam("id")Integer id, Session session){
        this.session = session;
        this.id = id;
        webSocketSet.add(this);
        addOnlineCount();
        log.info("用户:{}加入！当前在线人数为{}" ,id, getOnlineCount());
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose() {
        webSocketSet.remove(this);
        subOnlineCount();
        log.info("用户:{},关闭！当前在线人数为{}" ,getId(), getOnlineCount());
    }

    /**
     * 收到客户端消息后调用的方法
     *
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message) {
        log.info("来自客户端的消息:" + message);
        //群发消息
        for (WebSocketServer item : webSocketSet) {
            try {
                item.sendMessage(message);
            } catch (IOException e) {
                e.printStackTrace();
                continue;
            }
        }
    }

    /**
     * 发生错误时调用
     *
     * @param error
     */
    @OnError
    public void onError(Throwable error) {
        log.error("webSocket发生错误:",error.getMessage());
        error.printStackTrace();
    }

    /**
     * 这个方法与上面几个方法不一样。没有用注解，是根据自己需要添加的方法。
     *
     * @param message
     * @throws IOException
     */
    public void sendMessage(String message) throws IOException {
        //同步阻塞
        this.session.getBasicRemote().sendText(message);
        log.info("给用户{}推送了消息：{}",id,message);
        //异步
        //this.session.getAsyncRemote().sendText(message);
    }

    /**
     * 群发消息
     * @param message
     */
    public void sendToAll(String message){
        for (WebSocketServer webSocketServer : webSocketSet){
            try {
                webSocketServer.sendMessage(message);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }


~~~

对于实时通信，可以利用Stomp+MQ的方式来代替MQ+WebSocket发送消息





### 【JWT介绍以及可能出现的问题】  

**介绍**：

- **概念：**

  JWT（Json web token）,是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).定义了一种简洁的，自包含的方法用于通信双方之间以**JSON**对象的形式安全的传递信息。因为数字签名的存在，这些信息是可信的，JWT可以使用HMAC算法或者是RSA的公私秘钥对进行签名。

- **请求流程**：

  1. 用户使用账号和面发出post请求；
  2. 服务器使用私钥创建一个jwt；
  3. 服务器返回这个jwt给浏览器；
  4. 浏览器将该jwt串在请求头中像服务器发送请求；
  5. 服务器验证该jwt；
  6. 返回响应的资源给浏览器

- **使用场景：**

  身份认证，目前在单点登录（SSO）中比较广泛的使用了该技术

- **优点：**

  - **简洁(Compact):** 可以通过URL，POST参数或者在HTTP header发送，因为数据量小，传输速度也很快
  - **自包含(Self-contained)：**负载中包含了所有用户所需要的信息，避免了多次查询数据库
  - 因为Token是以JSON加密的形式保存在客户端的，所以JWT是跨语言的，原则上任何web形式都支持。
  - 不需要在服务端保存会话信息，特别适用于分布式微服务。

**问题：**

- **注销登录等场景下 token 还有效**

  比如退出登录、修改密码后token还有效

- **token 的续签问题**

  token 有效期一般都建议设置的不太长，那么 token 过期后如何认证，如何实现动态刷新 token，避免用户经常需要重新登录