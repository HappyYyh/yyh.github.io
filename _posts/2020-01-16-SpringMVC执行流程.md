---
title: SpringMvc执行流程
tags: ["Spring"]
---

## 一、入口

众所周知，Servlet是由tomcat接收http请求然后转成可识别的HttpServlet去处理；

SpringMvc的请求入口是`DispatcherServlet`，类图如下所示：

![image-20200116092236123](http://image.yangyhao.top/blog/SpringMvc执行流程-一-1.png)

可以看到`DispatcherServlet`是HttpServelt的子类，所以我们依旧可以从doService方法进行源码追踪。

doService里面会进入doDispatch方法

~~~java
try {
	doDispatch(request, response);
}
~~~

`doDispatch`:

~~~java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    //request对象
    HttpServletRequest processedRequest = request;
    // ...

    // Determine handler for the current request.
    // 根据request对象获取 HandlerExecutionChain
    mappedHandler = getHandler(processedRequest);

    // 根据mappedHandler获取HandlerAdapter
    // Determine handler adapter for the current request.
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());


    //执行方法，返回modelAndView
    // Actually invoke the handler.
    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

    //获取视图解析器
    processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
	//...
}
~~~





## 二、获取HandlerExecutionChain

由上面的getHandler方法进入，可以看到是根据handlerMappings去寻找到HandlerExecutionChain

~~~java
   /**
	 * Return the HandlerExecutionChain for this request.
	 * <p>Tries all handler mappings in order.
	 * @param request current HTTP request
	 * @return the HandlerExecutionChain, or {@code null} if no handler could be found
	 */
	@Nullable
	protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
		if (this.handlerMappings != null) {
			for (HandlerMapping mapping : this.handlerMappings) {
				HandlerExecutionChain handler = mapping.getHandler(request);
				if (handler != null) {
					return handler;
				}
			}
		}
		return null;
	}
~~~

由于handlerMappings是接口，其真正的实现是**AbstractHandlerMappings**,其源码暂时不展示了（太多了。。。）其主要原理是根据请求的URL、HTTP协议方法、请求头、请求参数、Cookie等寻找到合适的handler

根据debug可以看到，找到的handler里面已经包含了我们所写的controller的信息了

![image-20200116093934255](http://image.yangyhao.top/blog/SpringMvc%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B-%E4%B8%89-1.png)



## 三、找到HandlerAdapter

获取完HandlerExecutionChain以后，如果为空则返回我们常见的错误信息

~~~java
protected void noHandlerFound(HttpServletRequest request, HttpServletResponse response) throws Exception {
		if (pageNotFoundLogger.isWarnEnabled()) {
            // 这个很熟悉
			pageNotFoundLogger.warn("No mapping for " + request.getMethod() + " " + getRequestUri(request));
		}
		if (this.throwExceptionIfNoHandlerFound) {
			throw new NoHandlerFoundException(request.getMethod(),getRequestUri(request),
					new ServletServerHttpRequest(request).getHeaders());
		}
		else {
			response.sendError(HttpServletResponse.SC_NOT_FOUND);
		}
	}
~~~

如果不为空则会执行：

~~~java
// Determine handler adapter for the current request.
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
~~~

~~~java
   /**
	 * Return the HandlerAdapter for this handler object.
	 * @param handler the handler object to find an adapter for
	 * @throws ServletException if no HandlerAdapter can be found for the handler. This is a fatal error.
	 */
	protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
		if (this.handlerAdapters != null) {
			for (HandlerAdapter adapter : this.handlerAdapters) {
				if (adapter.supports(handler)) {
					return adapter;
				}
			}
		}
		throw new ServletException("No adapter for handler [" + handler +
				"]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
	}
~~~

## 四、方法执行

HandlerAdapter是一个适配器，它用统一的接口对各种Handler中的方法进行调用，即：

~~~java
// Actually invoke the handler.
mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
~~~

其内部核心源码如下：

~~~java
   /**
	 * Invoke the {@link RequestMapping} handler method preparing a {@link ModelAndView}
	 * if view resolution is required.
	 * @since 4.2
	 * @see #createInvocableHandlerMethod(HandlerMethod)
	 */
	@Nullable
	protected ModelAndView invokeHandlerMethod(HttpServletRequest request,HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {

        // ... 太多省略
        // 这里会进入调用
        invocableMethod.invokeAndHandle(webRequest, mavContainer);
        if (asyncManager.isConcurrentHandlingStarted()) {
            return null;
        }
        return getModelAndView(mavContainer, modelFactory, webRequest);
		
}
~~~

~~~java
   /**
	 * Invoke the method and handle the return value through one of the
	 * configured {@link HandlerMethodReturnValueHandler HandlerMethodReturnValueHandlers}.
	 * @param webRequest the current request
	 * @param mavContainer the ModelAndViewContainer for this request
	 * @param providedArgs "given" arguments matched by type (not resolved)
	 */
	public void invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContainer mavContainer,Object... providedArgs) throws Exception {
        
        // 这里已经进行了方法执行并且获取了返回值
		Object returnValue = invokeForRequest(webRequest, mavContainer, providedArgs);
		setResponseStatus(webRequest);

		if (returnValue == null) {
			if (isRequestNotModified(webRequest) || getResponseStatus() != null || mavContainer.isRequestHandled()) {
				disableContentCachingIfNecessary(webRequest);
				mavContainer.setRequestHandled(true);
				return;
			}
		}
		// ... 太多后面省略
	}
~~~

## 五、获取ModelAndView

方法调用后会返回一个mv对象即ModelAndView

~~~java
return getModelAndView(mavContainer, modelFactory, webRequest);
~~~

~~~java
@Nullable
	private ModelAndView getModelAndView(ModelAndViewContainer mavContainer,
			ModelFactory modelFactory, NativeWebRequest webRequest) throws Exception {

		modelFactory.updateModel(webRequest, mavContainer);
		if (mavContainer.isRequestHandled()) {
            // 貌似对于responseBody会走这里
			return null;
		}
		ModelMap model = mavContainer.getModel();
		ModelAndView mav = new ModelAndView(mavContainer.getViewName(), model, mavContainer.getStatus());
		if (!mavContainer.isViewReference()) {
			mav.setView((View) mavContainer.getView());
		}
		if (model instanceof RedirectAttributes) {
			Map<String, ?> flashAttributes = ((RedirectAttributes) model).getFlashAttributes();
			HttpServletRequest request = webRequest.getNativeRequest(HttpServletRequest.class);
			if (request != null) {
				RequestContextUtils.getOutputFlashMap(request).putAll(flashAttributes);
			}
		}
		return mav;
	}
~~~



## 六、ViewResover渲染

如果ModelAndView不为空则会进行视图渲染

~~~java
   /**
	 * Handle the result of handler selection and handler invocation, which is
	 * either a ModelAndView or an Exception to be resolved to a ModelAndView.
	 */
	private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv,
			@Nullable Exception exception) throws Exception {
        // ...
		// Did the handler return a view to render?
		if (mv != null && !mv.wasCleared()) {
            // 渲染
			render(mv, request, response);
			if (errorView) {
				WebUtils.clearErrorRequestAttributes(request);
			}
		}
        // ...
	}
~~~

render方法：

~~~java
   /**
	 * Render the given ModelAndView.
	 * <p>This is the last stage in handling a request. It may involve resolving the view by name.
	 * @param mv the ModelAndView to render
	 * @param request current HTTP servlet request
	 * @param response current HTTP servlet response
	 * @throws ServletException if view is missing or cannot be resolved
	 * @throws Exception if there's a problem rendering the view
	 */
	protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
		// ...
		View view;
		String viewName = mv.getViewName();
		if (viewName != null) {
			// We need to resolve the view name.
			view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
			if (view == null) {
				throw new ServletException("Could not resolve view with name '" + mv.getViewName() +"' in servlet with name '" + getServletName() + "'");
			}
		}else {
       // No need to lookup: the ModelAndView object contains the actual View object.
			view = mv.getView();
			if (view == null) {
				throw new ServletException("ModelAndView [" + mv + "] neither contains a view name nor a " +"View object in servlet with name '" + getServletName() + "'");
			}
		}
	}
~~~

获取到view对象后会进行view的render方法(这个还是在上面的render方法里面)：

~~~java
try {
    if (mv.getStatus() != null) {
        response.setStatus(mv.getStatus().value());
    }
    view.render(mv.getModelInternal(), request, response);
}
~~~

## 七、总结

最后，附上一张网上转载的[流程图解](https://frank-lam.github.io/fullstack-tutorial/#/JavaArchitecture/07-JavaWeb?id=_1-spring-mvc%e7%9a%84%e5%b7%a5%e4%bd%9c%e5%8e%9f%e7%90%86)

- ①客户端的所有请求都交给前端控制器DispatcherServlet来处理，它会负责调用系统的其他模块来真正处理用户的请求。 
- ② DispatcherServlet收到请求后，将根据请求的信息（包括URL、HTTP协议方法、请求头、请求参数、Cookie等）以及HandlerMapping的配置找到处理该请求的Handler（任何一个对象都可以作为请求的Handler）。 
- ③ 在这个地方Spring会通过HandlerAdapter对该处理进行封装。 
- ④ HandlerAdapter是一个适配器，它用统一的接口对各种Handler中的方法进行调用。 
- ⑤ Handler完成对用户请求的处理后，会返回一个ModelAndView对象给DispatcherServlet，ModelAndView顾名思义，包含了数据模型以及相应的视图的信息。 
- ⑥ ModelAndView的视图是逻辑视图，DispatcherServlet还要借助ViewResolver完成从逻辑视图到真实视图对象的解析工作。 
- ⑦ 当得到真正的视图对象后，DispatcherServlet会利用视图对象对模型数据进行渲染。 
- ⑧ 客户端得到响应，可能是一个普通的HTML页面，也可以是XML或JSON字符串，还可以是一张图片或者一个PDF文件。

![](http://image.yangyhao.top/blog/SpringMvc执行流程-七-1.png)