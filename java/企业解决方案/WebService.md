## WebService、SSO

WS：一种比较常用的接口方式，目的，只是为了传输数据或者接口API(接口地址、接口名称、接口参数、接口返回值)

```
传输的数据格式，多数是XML，但是ws中解析xml的方式比较特殊，而且是固定的，使用的技术使WSDL（XML解析语言）


WSDL 是基于 XML 的用于描述 Web Services 以及如何访问 Web Services 的语言。
WSDL 指网络服务描述语言
WSDL 使用 XML 编写
WSDL 是一种 XML 文档
WSDL 用于描述网络服务
WSDL 也可用于定位网络服务
WSDL 还不是 W3C 标准
```

普通的接口格式：

```
http://localhost:8080/queryAllUser.do
```

webservice接口格式：

```
http://localhost:8080/queryAllUser.do?WSDL


返回值：

    数据格式：

    <list>
        <userinfo1>
            <username>sfasdf</username>
        </userinfo1>
        <userinfo2>
            <username>sfasdadsfasdff</username>
        </userinfo2>
    </list>


    接口API方式：

        <list>
            <interface1>
                <methodname>sfasdf</methodname>
                <param>name</param>
            </interface1>
            <interface2>
                <methodname>sfasdadsfasdff</methodname>
                <param>phone</param>
            </interface2>
        </list>
```

应用场景：

```
1：调用第三方平台接口
2：分布式项目部署
```

测试工具：

```
soapUI用来测试WS接口的通用、负载、功能性
```

WS参考地址：

## Web Services---初级篇1

Original *2017-08-18* *康哥* [码神联盟](https://mp.weixin.qq.com/s?__biz=MzIxMjg4NDU1NA==&mid=2247483975&idx=1&sn=95fa1b5007cb6f7b271919994b216538&chksm=97be0f62a0c98674cae9b2db0aab2b267f719691d30fa6e328e1025e956ad9f23077434720e7&scene=21##)

![img](0-1525220251426.gif)

## 1前言

通过使用 Web Services，您的应用程序可以向全世界发布信息，或提供某项功能。Web Services 脚本平台需支持 XML + HTTP。

## 2web services 简介

------

​        Web Services 可使您的应用程序成为 Web 应用程序。

​        Web Services 通过 Web 进行发布、查找和使用。

- Web Services 是应用程序组件
- Web Services 使用开放协议进行通信
- Web Services 是独立的（self-contained）并可自我描述
- Web Services 可通过使用UDDI来发现
- Web Services 可被其他应用程序使用
- XML 是 Web Services 的基础
- ​

## 3应用知识点

- ​
- HTML
- XML
- ​

## 4如何工作

基础的 Web Services 平台是 XML + HTTP。

HTTP 协议是最常用的因特网协议。

XML 提供了一种可用于不同的平台和编程语言之间的语言。

## 5平台元素

- ​
- SOAP (简易对象访问协议)
- UDDI (通用描述、发现及整合)
- WSDL (Web services 描述语言)
- ​

## 6优势

## **最重要的事情是协同工作**

由于所有主要的平台均可通过 Web 浏览器来访问 Web，不同的平台可以借此进行交互。为了让这些平台协同工作，Web 应用程序被开发了出来。

Web 应用程序是运行在 Web 上的简易应用程序。它们围绕 Web 浏览器标准被进行构建，几乎可被任何平台之上的任何浏览器来使用。

通过使用 Web services，您的应用程序可向全世界发布功能或消息。

Web services 使用 XML 来编解码数据，并使用 SOAP 借由开放的协议来传输数据。

通过 Web services，您的会计部门的 Win 2k 服务器可与 IT 供应商的 UNIX 服务器进行连接。

## 7应用组件

## **Web services 有两种类型的应用**

### **可重复使用的应用程序组件**

有一些功能是不同的应用程序常常会用到的。那么为什么要周而复始地开发它们呢？

Web services 可以把应用程序组件作为服务来提供，比如汇率转换、天气预报或者甚至是语言翻译等等。

比较理想的情况是，每种应用程序组件只有一个最优秀的版本，这样任何人都可以在其应用程序中使用它。

### 连接现有的软件

通过为不同的应用程序提供一种链接其数据的途径，Web services有助于解决协同工作的问题。

通过使用 Web services，您可以在不同的应用程序与平台之间来交换数据。

## 8SOAP

基本的 Web services 平台是 XML + HTTP。

- SOAP 指简易对象访问协议
- SOAP 是一种通信协议
- SOAP 用于应用程序之间的通信
- SOAP 是一种用于发送消息的格式
- SOAP 被设计用来通过因特网进行通信
- SOAP 独立于平台
- SOAP 独立于语言
- SOAP 基于 XML
- SOAP 很简单并可扩展
- SOAP 允许您绕过防火墙
- SOAP 将作为 W3C 标准来发展
- ​

## 9WSDL

WSDL 是基于 XML 的用于描述 Web Services 以及如何访问 Web Services 的语言。

- WSDL 指网络服务描述语言
- WSDL 使用 XML 编写
- WSDL 是一种 XML 文档
- WSDL 用于描述网络服务
- WSDL 也可用于定位网络服务
- WSDL 还不是 W3C 标准
- ​

## 10UDDI

UDDI 是一种目录服务，通过它，企业可注册并搜索 Web services。

- UDDI 指通用的描述、发现以及整合（Universal Description, Discovery and Integration）。
- UDDI 是一种用于存储有关 web services 的信息的目录。
- UDDI 是一种由 WSDL 描述的网络服务接口目录。
- UDDI 经由 SOAP 进行通迅。
- UDDI 被构建于 Microsoft .NET 平台之中。

