###  解决：The requested resource does not support http method 'GET'

有以下 ASP.NET WebAPI 代码：
``` csharp
public string Version()
{
    return "1.0";
}
```
路由配置为： ***api/{controller}/{action}/{id}***  
当使用 *GET* 方法请求该 Action 时，ASP.NET WebAPI 却返回：
``` csharp
{
  "Message": "The requested resource does not support http method 'GET'."
}
```
原因是，如果 Action 没有配置 **HttpMethod Attribute**，则 Action 默认只接受 Post 请求。  
但是当我们把方法名修改为 **GetVersion** 时，则可以请求成功。  
这是因为以 *Get...* 开头的方法，Web API 就会视为接受 Get 请求，同样的，名字以 *Put...*,*Post...*,*Delete...* 开头的方法就意味着接受相应的 HTTP 请求，不需要添加 Attribute 如 *HttpPostAttribute*, *HttpPutAttribute*, *HttpDeleteAttribute*， Web API 在选择 Action 时就会考虑它们。

所以 希望 Action 接受 *Get* 方法，有以下两种方式：
```csharp
[HttpGet]
public string Version()
{
    return "1.0";
}
```
或者
```csharp
public string GetVersion()
{
    return "1.0";
}
```