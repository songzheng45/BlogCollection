### 【翻译】ASP.NET Web API 2 中的全局错误处理


原文链接 [Global Error Handling in ASP.NET Web API 2](https://docs.microsoft.com/en-us/aspnet/web-api/overview/error-handling/web-api-global-error-handling)  
由于翻译水平有限，如果感觉翻译不通顺的地方，请对照原文，如果有翻译错误，还请不吝指正，共同进步！

----------------------------

直到今天在 Web API 中也没有一种简单的方式来记录或处理全局的错误。一些未处理的异常可以通过 [Exception Filters](https://docs.microsoft.com/en-us/aspnet/web-api/overview/error-handling/exception-handling) 处理，但是还有许多 Exception Filters 不能处理的情况 。例如：
1. 来自 Controller 构造器的异常。
2. 来自 Message Handlers 的异常。
3. 匹配路由过程中的异常
4. 在序列化响应内容期间产生的异常

我们想提供一种简单、一致的方法去记录和处理（在可能的情况下）这些异常。

处理异常有两种主要的情形：发送一个包含错误消息的响应，或者用日志记录异常。后者的一个例子是异常在将响应内容转换为网络流时被抛出，在这种情况下，发送一个新的响应消息已经晚了，因为状态码、响应头和内容部分已经确定下来了，于是我们只能粗暴地终止连接。
即使异常不能作为一个新的响应消息去处理，我们仍然支持记录异常。一旦我们检测到一个错误，我们可以返回一个适当的错误响应，如下所示：
``` csharp
public IHttpActionResult GetProduct(int id)
{
    var product = products.FirstOrDefault((p) => p.Id == id);
    if (product == null)
    {
        return NotFound();
    }
    return Ok(product);
}
```

### 已存在的选项
除了 [exception filters](https://docs.microsoft.com/en-us/aspnet/web-api/overview/error-handling/exception-handling)，[message handlers](https://docs.microsoft.com/en-us/aspnet/web-api/overview/advanced/http-message-handlers) 也可以用来观察所有 500 级别的响应，但是对这些响应做更改却是困难的，因为它们缺少关于原始错误的上下文。对于能处理的异常情况， message handlers 也存在一些和 exception filters 同样的限制。  
尽管Web API 的具有可以捕获错误的跟踪基础架构，但跟踪基础架构用于诊断目的，不是设计用来或不适用于在生产环境中运行。  
应该提供这样的全局异常处理和日志记录，它们可以在生产环境中运行，并且可以添加到现有的监控解决方案（例如， [ELMAH](https://code.google.com/p/elmah/)）。

### 解决方案预览
我们提供两种新的用户可替换的方案， [IExceptionLogger](https://docs.microsoft.com/en-us/aspnet/web-api/overview/releases/whats-new-in-aspnet-web-api-21) 和 IExceptionHandler，去记录和处理未处理的异常。这两个方案很相似，但是有两个区别：  
1. 支持注册多个 Exception Loggers，但是只能有一个 ExceptionHandler。
2. Exception Loggers 总是会被调用，即使我们即将终止请求。 Exception Handlers 只在当我们仍然可以选择要发送的响应消息时被调用。

两种服务都提供访问包含检测到异常时的关键信息的异常上下文，特别是 [HttpRequestMessage](https://msdn.microsoft.com/en-us/library/system.net.http.httprequestmessage(v=vs.110).aspx)，[HttpRequestContext](https://msdn.microsoft.com/en-us/library/system.web.http.controllers.httprequestcontext(v=vs.118).aspx)，抛出的异常和异常来源（详情往下看）。

### 设计原则
1. **没有重大更改** 因为这个功能将要被添加到次要版本中，一个影响解决方案的重要约束是不能有重大更改，无论是对原有类型定义还是行为。这个约束条件使我们放弃了一些清理工作，即在现有的catch块中将异常转换为500响应（in terms of existing catch blocks turning exceptions into 500 responses）。我们可能会考虑在下个主版本中加入这个额外的清理功能。如果你觉得这个功能很重要，可以在 [ASP.NET Web API user voice](http://aspnet.uservoice.com/forums/147201-asp-net-web-api/suggestions/5451321-add-flag-to-enable-iexceptionlogger-and-iexception) 投票。
2. **保持 Web API 架构一致性**Web API的过滤器管道是一种处理跨领域问题的好方法，可以灵活地在特定于操作，控制器或全局范围内应用逻辑。包括异常过滤器在内，即使注册在全局范围，过滤器总是有 action 和 controller 的上下文，。这个契约对过滤器有用，但是又意味着异常过滤器即使注册在全局范围内，对一些异常情况也并不适用，例如来自 message handlers 的异常，不存在 action 或 controller 的上下文。如果我们想利用过滤器提供的灵活性去处理异常，我们就仍然需要它。但是如果我们需要在一个 controller 上下文之外处理异常，我们也需要一个和全局错误处理程序分离的结构（一些没有 controller 和 action 上下文约束的东西）。

### 什么时候去用
- Exception loggers 是查看 Web API 捕获的所有未处理的异常的解决方案。
- Exception handlers 是定制 Web API 捕获的所有未处理的异常的可能响应的解决方案。
- Exception filters 是处理与特定 action 或 controller 相关的未处理异常子集的最简单的解决方案。

### 方案详情
异常日志记录器和处理程序服务接口是简单的异步方法，带有各自的上下文。
``` csharp
public interface IExceptionLogger
{
   Task LogAsync(ExceptionLoggerContext context, 
                 CancellationToken cancellationToken);
}

public interface IExceptionHandler
{
   Task HandleAsync(ExceptionHandlerContext context, 
                    CancellationToken cancellationToken);
}
```
我们还为这两个接口提供基类。覆盖核心方法（异步 或 非异步）是必要的，这样就可以在推荐的时间去记录日志或做一些处理。用于记录的 **ExceptionLogger** 基类会确保核心方法对每个异常只被调用一次（即使它稍后在调用堆栈中进一步传播并再次被捕获）。 **ExceptionHandler** 基类将调用核心处理方法，并且只处理调用堆栈栈顶的异常，忽略旧的嵌套的catch块。（这些基类的简化版本在下文附录部分）  
**IExceptionLogger** 和 **IExceptionHandler** 都通过 **ExceptionContext** 接收有关异常的信息。
``` csharp
public class ExceptionContext
{
   public Exception Exception { get; set; }

   public HttpRequestMessage Request { get; set; }

   public HttpRequestContext RequestContext { get; set; }

   public HttpControllerContext ControllerContext { get; set; }

   public HttpActionContext ActionContext { get; set; }

   public HttpResponseMessage Response { get; set; }

   public string CatchBlock { get; set; }

   public bool IsTopLevelCatchBlock { get; set; }
}
```
当框架调用一个 exception logger 或一个 exception handler时，它总是会提供一个 **Exception** 和一个 **Request** 。此外为了单元测试，它还会一直提供一个 **RequestContext**。它很少会提供 **ControllerContext** 和 **ActionContext** （只有当从catch块调用异常过滤器时），也很少会提供 **Response** （只有在某些 IIS 的情况下，在中间尝试写响应）。需要注意的是，由于其中一些属性可能是 **null** ，它是由使用者在访问异常类的属性之前检查 **null** 。 **CatchBlock** 是一个字符串，用来表示哪个catch块查看到异常。 catch block 字符串有如下值：
- HttpServer（SendAsync 方法）
- HttpControllerDispatcher（SendAsync 方法）
- HttpBatchHandler（SendAsync 方法）
- OWIN host：
	- HttpMessageHandlerAdapter.BufferResponseContentAsync (for buffering output)
	- HttpMessageHandlerAdapter.CopyResponseContentAsync (for streaming output)
- Web host:
	- HttpControllerHandler.WriteBufferedResponseContentAsync (for buffering output)
	- HttpControllerHandler.WriteStreamedResponseContentAsync (for streaming output)
	- HttpControllerHandler.WriteErrorResponseContentAsync (for failures in error recovery under buffered output mode)

catch 块字符串列表也可通过静态只读属性访问（核心的 catch 块字符串在静态类 ExceptionCatchBlocks 中，其余的分别定义在针对 OWIN 托管和 Web 托管的静态类中）。  
**IsTopLevelCatchBlock ** 有助于遵守异常处理时的推荐模式，即只处理调用堆栈顶部的异常。  
exception handler 允许异常传播，直到它们即将被宿主看到，而不是在任何地方就将异常转换为500响应。  

除了 **ExceptionContext** 之外，日志记录器还通过 **ExceptionLoggerContext** 获取更多信息。
``` csharp
public class ExceptionLoggerContext
{
   public ExceptionContext ExceptionContext { get; set; }
   public bool CanBeHandled { get; set; }
}
```
第二个属性 **CanBeHandled** 允许一个日志记录器识别一个不能被处理的异常。当连接即将被终止并且没有新的响应消息被发送时，记录器就会被调用，但是处理程序不会被调用，而日志记录器就可以利用这个属性识别这个场景。

除了 **ExceptionContext** 之外，异常处理程序还从 **ExceptionHandlerContext** 中获取更多它能够设置的属性去处理异常：
``` csharp
public class ExceptionHandlerContext
{
   public ExceptionContext ExceptionContext { get; set; }
   public IHttpActionResult Result { get; set; }
}
```
一个异常处理程序通过设置 **Result** 属性为一个 action result 来表示它已经处理过一个异常了，比如一个 [ExceptionResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.exceptionresult(v=vs.118).aspx)，[InternalServerErrorResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.internalservererrorresult(v=vs.118).aspx)，[StatusCodeResult](https://msdn.microsoft.com/en-us/library/system.web.http.results.statuscoderesult(v=vs.118).aspx) 或者一个自定义result。 如果 **Result** 属性是 null，那么这个异常不被处理并且会抛出最原始的异常。

对于调用堆栈顶部的异常，我们采取了额外的步骤，以确保响应适合API调用者。如果异常传播到宿主，调用者将看到报错的黄色页面或一些其他宿主提供的响应，通常是HTML，通常不是合适的API错误响应。
在这些情况下，结果从非空开始，并且只有当自定义异常处理程序显式地将其设置为空（未处理）时，异常才会传播到宿主。
在这种情况下将 **Result** 设置为 null 对于两种情况可能有用：
1. OWIN托管Web API与在Web API之前/之外注册的自定义异常处理中间件。
2. 通过浏览器进行本地调试，其中报错的黄色页面实际上是对未处理异常的有帮助的响应。

对于 exception loggers 和 exception handlers 自身抛出的异常，我们不会做任何恢复（除了让异常传播，你也可以在页面底部留下你更好的方法）。
exception loggers 和 exception handlers 的契约设计是它们不应该让异常传播到它们的调用者，否则的话异常会一直传播到宿主，并返回给客户端一个HTML错误（像 ASP.NET 的黄色报错页面），而这一般并不是API调用者期待的 JSON 或 XML 。

PS.  contract 契约，有本书名叫《Design by contract》翻译为《契约式设计》，有评论认为
>“在我看来Design by contract不是一种编程范型，它是一种设计风格，一种语法规范，甚至是个商标

这里我个人觉得可以理解为一种设计风格。
>参考： 
>维基百科 [契约式设计](https://zh.wikipedia.org/wiki/%E5%A5%91%E7%BA%A6%E5%BC%8F%E8%AE%BE%E8%AE%A1)
>知乎讨论 [怎样解释 Design by Contract (契约式设计)?](https://www.zhihu.com/question/19864652)

### 示例
#### 追踪 Exception Logger
以下的 exception logger 将异常数据发送给 Trace 源（包括Visual Sutido 中的调试输出窗口）。
``` csharp
class TraceExceptionLogger : ExceptionLogger
{
	public override void LogCore(ExceptionLoggerContext context)
	{
		Trace.TraceError(context.ExceptionContext.Exception.ToString());
	}
}
```

#### 自定义错误消息异常处理程序
以下代码给客户端产生一个自定义的错误响应，包含一个联系支持的邮箱地址。
``` csharp
class OopsExceptionHandler : ExceptionHandler
{
    public override void HandleCore(ExceptionHandlerContext context)
    {
        context.Result = new TextPlainErrorResult
        {
            Request = context.ExceptionContext.Request,
            Content = "Oops! Sorry! Something went wrong." +
                      "Please contact support@contoso.com so we can try to fix it."
        };
    }

    private class TextPlainErrorResult : IHttpActionResult
    {
        public HttpRequestMessage Request { get; set; }

        public string Content { get; set; }

        public Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = 
                             new HttpResponseMessage(HttpStatusCode.InternalServerError);
            response.Content = new StringContent(Content);
            response.RequestMessage = Request;
            return Task.FromResult(response);
        }
    }
}
```

#### 如何使用定制 ExceptionLogger 和 ExceptionHandler

添加定制 ExceptionLogger 如下：
``` csharp
webConfiguration.Services.Add(typeof(IExceptionLogger), new MyExceptionLogger()); 
```
添加定制 Exception Handler 如下：
``` csharp
webConfiguration.Services.Replace(typeof(IExceptionHandler), new MyExceptionHandler()); 
```
只能用 **Replace()** 方法因为 Web API 仅支持一个 handler。

PS. 其中 webConfiguration 是 System.Web.Http.HttpConfiguration 的一个实例。


### 附录： 基类详情
``` csharp
public class ExceptionLogger : IExceptionLogger
{
    public virtual Task LogAsync(ExceptionLoggerContext context, 
                                 CancellationToken cancellationToken)
    {
        if (!ShouldLog(context))
        {
            return Task.FromResult(0);
        }

        return LogAsyncCore(context, cancellationToken);
    }

    public virtual Task LogAsyncCore(ExceptionLoggerContext context, 
                                     CancellationToken cancellationToken)
    {
        LogCore(context);
        return Task.FromResult(0);
    }

    public virtual void LogCore(ExceptionLoggerContext context)
    {
    }

    public virtual bool ShouldLog(ExceptionLoggerContext context)
    {
        IDictionary exceptionData = context.ExceptionContext.Exception.Data;

        if (!exceptionData.Contains("MS_LoggedBy"))
        {
            exceptionData.Add("MS_LoggedBy", new List<object>());
        }

        ICollection<object> loggedBy = ((ICollection<object>)exceptionData[LoggedByKey]);

        if (!loggedBy.Contains(this))
        {
            loggedBy.Add(this);
            return true;
        }
        else
        {
            return false;
        }
    }
}

public class ExceptionHandler : IExceptionHandler
{
    public virtual Task HandleAsync(ExceptionHandlerContext context, 
                                    CancellationToken cancellationToken)
    {
        if (!ShouldHandle(context))
        {
            return Task.FromResult(0);
        }

        return HandleAsyncCore(context, cancellationToken);
    }

    public virtual Task HandleAsyncCore(ExceptionHandlerContext context, 
                                       CancellationToken cancellationToken)
    {
        HandleCore(context);
        return Task.FromResult(0);
    }

    public virtual void HandleCore(ExceptionHandlerContext context)
    {
    }

    public virtual bool ShouldHandle(ExceptionHandlerContext context)
    {
        return context.ExceptionContext.IsOutermostCatchBlock;
    }
}
```
