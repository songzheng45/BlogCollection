# ASP.NET WebAPI （反）序列化用[SerializableAttribute]修饰的类的一个坑

## 发现问题
在 ASP.NET WebAPI 项目中，有这样的 ViewModel 类：
``` csharp
[Serializable]
class Product
{
    public int Id { get; set; }
    public decimal Price { get; set; }
    public DateTime ProductDate { get; set; }
}
```
Controller 和 Action 代码如下：
``` csharp
public class ProductController : ApiController
{
    public Product Get(int id)
    {
        return new Product()
        {
            Id = 1,
            Price = 12.9m,
            ProductDate = new DateTime(1992, 1, 1)
        };
    }
}
```
客户端请求该资源： `http://localhost:5000/api/product/1`，结果发现 WebAPI 返回这样的JSON，如下：
``` JSON
{
  "<Id>k__BackingField": 1,
  "<Price>k__BackingField": 12.9,
  "<ProductDate>k__BackingField": "1992-01-01T00:00:00"
}
```

我们知道，自动属性虽然没有定义字段，但是C#编译器会生成相应的私有字段，类似 `private int <Id>k__BackingField`。    
我们期望 WebAPI 序列化时将属性名作为 JSON 的键，而这里 WebAPI 序列化的却是编译器生成的私有字段，显然不符合我们的要求。  

奇怪的地方是，如果单独用 Json.NET 类库去序列化，则能得到期望的 JSON，如下：
``` Json
{
  "Id": 1,
  "Price": 12.9,
  "ProductDate": "1992-01-01T00:00:00"
}
```

## 找到原因  
经过 Google 一番，原来是和 **SerializableAttribute** 有关。  
从 Json.NET 4.5 Release 2 版本开始，新增这样的特性：  
>如果检测到类型有 **SerializableAttribute**，将序列化该类型的所有私有/公开字段，并且忽略其属性。  
如果不想要这个新特性，可以对类应用 **JsonObjectAttribute** 来覆盖，或者在全局范围内 设置 **DefaultContractResolver** 的 **IgnoreSerializableAttribute** 为 *true*。  
而从 release 3 版本开始 **IgnoreSerializableAttribute** 默认为 *true*。  

而 ASP.NET WebAPI 依赖 Json.NET，但是将 **IgnoreSerializableAttribute** 设置为 *false*，也就是不忽略 **SerializableAttribute**，于是如果类用 **[SerializableAttribute]** 修饰，就只序列化和反序列化字段而忽略属性。  
参考： [Why won't Web API deserialize this but JSON.Net will?](http://stackoverflow.com/questions/12843972/why-wont-web-api-deserialize-this-but-json-net-will#comment-17382021)

## 解决问题
了解到问题的原因后，可以通过以下方式解决：
1. 去掉 [Serializable]
2. 应用 [JsonObjectAttribute]
3. 设置 **IgnoreSerializableAttribute**

最简单的方法就是去掉 **[Serializable]**，如果由于某些原因不能去除，可以用其他两种办法。

### 应用 JsonObjectAttribute
``` csharp
[Newtonsoft.Json.JsonObject]
[System.Serializable]
class Product
{
    public int Id { get; set; }
    public decimal Price { get; set; }
    public DateTime ProductDate { get; set; }
}
```

### 设置 **IgnoreSerializableAttribute**
``` csharp
public static class WebApiConfig
{
    public static void Register(HttpConfiguration config)
    {
        // 其他代码省略 .........

        // 将 SerializerSettings 重置为默认值，默认值中 IgnoreSerializableAttribute = true
        config.Formatters.JsonFormatter.SerializerSettings = new JsonSerializerSettings();
    }
}
```

------------------------
参考： http://james.newtonking.com/archive/2012/04/11/json-net-4-5-release-2-serializable-support-and-bug-fixes
