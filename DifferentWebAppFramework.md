整理目前流行的跨平台WebApp开发技术的特点，仅供参考。  

每个框架几乎都包含以下特性：
- 使用 HTML5 + CSS + JavaScript 开发
- 跨平台重用代码
- 丰富的UI库
- 提供访问设备原生API的 JavaScript API 包装器
- 解决原生开发中机型适配的难题
- 提供打包、部署的工具或服务
- 都需要学习自身封装的 JavaScript API

筛选框架的要求：
- 性能：运行速度快
- UI：提供接近原生的UI体验
- 插件多，文档丰富，开发效率高，容易扩展和维护
- 满足业务需求

## Cordova
>Cordova 和 PhoneGap 的区别？  
PhoneGap 是 Apache Cordova 的一个分发版，就像 Ubuntu 是 基于 Linux 的一个发行版，其代码库也基于 Cordova，只是 PhoneGap 关联了 Adobe 的一些额外的商业工具或服务，例如 PhoneGap Build 和 Adobe Shadow，来帮助开发者简化开发。  
此外，两者提供的CLI工具、项目结构有差异，如：  
Cordova 把 config.html 放在项目目录下，而 PhoneGap 把它放在www 目录下。

**优点**：
- 开源免费，社区生态成熟，插件丰富
- 支持离线场景应用
- 开发工具选择空间大

**缺点**:
- 只提供基础访问设备的接口，需要自己搭配其他UI框架和JavaScript框架来搭配



**UI框架**  
- [Framework7](https://github.com/nolimits4web/Framework7)
- [MSUI](http://m.sui.taobao.org/getting-started/) | [Github](https://github.com/sdc-alibaba/SUI-Mobile)
- Famous   
为任意屏幕创建流畅、复杂的UI。  
免费和开源的JavaScript渲染引擎。着名引擎的独特之处在于其JavaScript渲染引擎和3D物理引擎。  
[官网](http://deprecated.famous.org/) |  [Github](https://github.com/famous/engine) | [Hello World](http://famous.org/get-started.html)  
[使用 Famo.us 创建高性能移动 UI](https://www.ibm.com/developerworks/cn/web/wa-famous/)  

### 参考资料
[Cordova中文文档](http://cordova.axuer.com/docs/zh-cn/latest/)  
[创建第一个App（英文）](https://cordova.apache.org/docs/en/6.x/guide/cli/index.html)  
[利用 Cordova+Famous 创建高性能跨平台APP](http://www.ifcoder.us/1046?winzoom=1)  
[使用 Cordova 和 Vue.js 创建移动应用](https://gold.xitu.io/entry/57ea26f5da2f600060e59c5e)

## Ionic
官网地址：http://www.ionic.wang/  （有案例）
Ionic = Cordova + AngularJS + 一套样式库。

**技术要求**  
- HTML + CSS + AngularJS
 
**优点**：
- 基于 Cordova
- 漂亮的界面，追求性能，专注原生，免费开源
- Angular JS MVVM 开发理念，数据双向绑定 
- 继承自 Cordova，可以使用 Cordova 的插件

**缺点**：
- Angular JS 学习路线陡峭
- Ionic 框架相比于原生的 Cordova 有所差异，Cordova 某些官方插件可能不适用于Ionic


## AppCan

通过AppCan IDE集成开发系统、云端打包器等，快速开发出Android、iOS、WP平台上的移动应用。

有两种方式创建项目：IDE 和云端，并且IDE可以同步到云端。  
免费用户有100M空间、50个应用的限制。

**优点**
- 提供一体化解决方案，方便环境搭建、开发、调试、发布
- 框架自带UI包，包含常用控件样式
- 框架对UI、动画渲染进行过优化，反应速度快
- 支持本地打包、云端打包
- 基于密钥的代码加密

**缺点**
- 不开源，无法修改、优化底层代码 
- 分大众版和企业版，大众版免费，但功能有缺失，详细见附录
- 暂不支持自行开发控件/，无法调取android原生功能  
- 框架自带功能过多，导致应用安装包偏大。 
- 文档偏少  
- 部分系统无法使用IDE进行调试  
- 只能在服务器端发布，无法在本地发布  
- IOS发布，需要将证书上传至服务器

参考：   
[Phonegap VS AppCan](http://www.cnblogs.com/wanshutao/p/4242916.html)
 
**使用案例**：  
我爱我家App 等

**附录**：  
企业版和大众版主要有以下几点区别：  
- 开发环境：  
企业版走独立的开发环境与打包环境，企业版配备macmini打包服务器，可以实现本地环境下创建项目，调试，打包；  
大众版不管是创建项目还是打包都需要依托于官方的服务器，需要在联网的情况下进行，打包需要将源码上传到官方服务器进行打包；  
- 版本控制：企业版独立控制引擎插件的版本；  
   大众版官方统一维护，官方换哪个版本开发者就需要使用哪个版本，没有选择；  
- 协同开发：企业版可通过macmini后台分配开发者或者应用管理员帐号，可实现协同开发。  
   大众版不能满足协同开发
- 企业版有推送API接口  
   大众版没有
- 售后服务：企业版有独立的售后团队  
    大众版的入口是论坛  


## Dcloud

![Dcloud组件](http://img.blog.csdn.net/20160203110750192?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


![Dcloud和原生开发对比](http://img.blog.csdn.net/20160203110803395?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

特点：  
云编译必须联网获取AppId

优点：
- 国内厂商，中文文档
- 对HTML5的性能、工具、能力都做了深入扩展，提供 IDE 、云服务等帮助节省时间
- MUI 更贴近国内App使用习惯，提供模块的详细例子，如登录，个人中心

缺点：
- 部分操作需要具备原生开发经验，如离线打包App
- 新产品仍然有bug，还需改进


### 学习路线：
![DCloud学习路线](http://img.blog.csdn.net/20160111152440931?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


## APICloud

优点：
- 不懂原生开发，不懂后台语言就可完成APP

缺点：
- 更新速度快，版本不够稳定
- 面向不懂App开发人群，不适合程序员和科技公司，过度依赖会降低技术水平 
- 涉嫌抄袭DCloud大量代码


## React Native
能够在Javascript和React的基础上获得完全一致的开发体验，构建世界一流的原生APP。  
仅需学习一次，编写任何平台。(Learn once, write anywhere)  

缺点：  
- 初次学习成本高
- 必须在不同平台下写两套代码，依赖暴露的接口 


----------------------  

## 总结
经过一番对比，感觉 Cordova 和 DCloud 更适合本次项目。

原因：  
Cordova   
- 生态成熟，有更多可搭配工具使用，开源代码可自由定制；
- 前端框架： famous 或 Framework7
 
DCloud
- 国产中的开源，免费，性能不错
- 提供云服务帮助打包和部署、测试，降低一部分门槛，减少时间；
- 前端框架：MUI 


**其他框架不适合的原因**：  
APICloud
- 生态不好，名声不好，面向群体不适合；  

AppCan    
- 闭源，商业化产品，免费版限制太多；

Ionic
- AngularJS 学习曲线陡峭，需要时间；  

React Native
- 学习成本高
