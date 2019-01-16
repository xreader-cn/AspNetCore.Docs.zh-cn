---
uid: web-api/overview/advanced/dependency-injection
title: ASP.NET Web API 2 中的依赖关系注入 |Microsoft Docs
author: MikeWasson
description: 本教程演示如何将依赖项注入到你的 ASP.NET Web API 控制器。 在教程的 Web API 2 Unity 应用程序块中使用的软件版本...
ms.author: riande
ms.date: 01/20/2014
ms.assetid: e3d3e7ba-87f0-4032-bdd3-31f3c1aa9d9c
msc.legacyurl: /web-api/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 318a2f1c587feb360212a390bb5de7bdc127513d
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341821"
---
<a name="dependency-injection-in-aspnet-web-api-2"></a>ASP.NET Web API 2 中的依赖关系注入
====================
通过[Mike Wasson](https://github.com/MikeWasson)

[下载已完成的项目](http://code.msdn.microsoft.com/ASP-NET-Web-API-Tutorial-468ee148)

> 本教程演示如何将依赖项注入到你的 ASP.NET Web API 控制器。
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
> 
> 
> - Web API 2
> - [Unity 应用程序块](https://www.nuget.org/packages/Unity/)
> - Entity Framework 6 （也有效，版本 5）


## <a name="what-is-dependency-injection"></a>什么是依赖关系注入？

依赖项是另一个对象所需的任何对象。 例如，是常见定义[存储库](http://martinfowler.com/eaaCatalog/repository.html)用于处理数据访问。 让我们演示了通过一个示例。 首先，我们需要定义一个域模型：

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

下面是将项存储在数据库中，使用实体框架的简单存储库类。

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

现在让我们来定义支持 GET 请求的 Web API 控制器`Product`实体。 （我已经将文章和其他方法为简单起见。）下面是第一次尝试：

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

请注意，控制器类取决于`ProductRepository`，我们要让创建控制器和`ProductRepository`实例。 但是，它的原因是这种方式，依赖关系进行硬编码不是好方法。

- 如果你想要替换`ProductRepository`使用不同的实现，您还需要修改控制器类。
- 如果`ProductRepository`具有依赖关系，您必须配置这些控制器中。 对于具有多个控制器的大型项目，配置代码变得分散于整个项目。
- 很难进行单元测试，因为控制器是硬编码查询数据库。 对于单元测试时，应使用的 mock 或存根 （stub） 存储库，这是不可能成为当前设计。

我们可以解决这些问题由*注入*到控制器的存储库。 首先，重构`ProductRepository`到接口的类：

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

然后提供`IProductRepository`作为构造函数参数：

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

此示例使用[构造函数注入](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection)。 此外可以使用*setter 注入*，设置通过 setter 方法或属性的依赖项的位置。

但现在没有问题，因为你的应用程序不会直接创建控制器。 它将路由请求，和 Web API 不知道的任何内容时，web API 创建控制器`IProductRepository`。 这是 Web API 依赖关系解析程序的作用所在。

## <a name="the-web-api-dependency-resolver"></a>Web API 依赖关系解析程序

Web API 定义**IDependencyResolver**用于解析的依赖关系接口。 下面是该接口的定义：

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

**IDependencyScope**接口有两个方法：

- **GetService**创建类型的一个实例。
- **Getservices 进行提取**创建指定类型的对象的集合。

**IDependencyResolver**方法继承**IDependencyScope** ，并将添加**如何**方法。 我将在本教程后面讨论的作用域。

当 Web API 创建一个控制器实例时，它将首先调用**IDependencyResolver.GetService**、 传入的控制器类型。 此可扩展性挂钩可用于创建控制器，解析任何依赖项。 如果**GetService**返回 null，Web API 控制器类上的无参数构造函数的查找。

## <a name="dependency-resolution-with-the-unity-container"></a>利用 Unity 容器解析依赖项

尽管您可以编写的完整**IDependencyResolver**从零开始，该接口的实现真正旨在充当 Web API 和现有的 IoC 容器之间的桥。

IoC 容器是一个软件组件，负责管理依赖项。 您向容器注册类型，然后使用容器来创建对象。 容器自动找出的依赖关系。 多个 IoC 容器还可用于控制对象生存期和作用域等。

> [!NOTE]
> "IoC"代表"控制反转"，这是一个框架，其中调用到应用程序代码中的一般模式。 IoC 容器构造您的对象，其中"反转"常用控制流。


对于本教程中，我们将使用[Unity](https://msdn.microsoft.com/library/ff647202.aspx)从 Microsoft 模式&amp;实践。 (其他常用库包括[Castle Windsor](http://www.castleproject.org/)， [Spring.Net](http://www.springframework.net/)， [Autofac](https://code.google.com/p/autofac/)， [Ninject](http://www.ninject.org/)，和[StructureMap](http://structuremap.github.io/documentation/).)可以使用 NuGet 包管理器安装 Unity。 从**工具**在 Visual Studio 中，选择菜单**NuGet 包管理器**，然后选择**程序包管理器控制台**。 在包管理器控制台窗口中，键入以下命令：

[!code-console[Main](dependency-injection/samples/sample7.cmd)]

下面是实现**IDependencyResolver**包装 Unity 容器。

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

> [!NOTE]
> 如果**GetService**方法无法解析类型，它应返回**null**。 如果**getservices 进行提取**方法不能将类型解析，则它应返回一个空集合对象。 不会引发异常的未知类型。


## <a name="configuring-the-dependency-resolver"></a>配置依赖关系解析程序

设置依赖关系解析程序上**DependencyResolver**属性的全局**HttpConfiguration**对象。

下面的代码寄存器`IProductRepository`使用 Unity 的接口，然后创建`UnityResolver`。

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

## <a name="dependency-scope-and-controller-lifetime"></a>依赖关系范围和控制器生命周期

创建控制器的每个请求。 若要管理对象生存期**IDependencyResolver**使用的概念*作用域*。

依赖关系解析程序附加到**HttpConfiguration**对象具有全局作用域。 当 Web API 创建一个控制器时，它将调用**如何**。 此方法返回**IDependencyScope**表示子范围。

然后调用 web API **GetService**子作用域，以创建控制器上。 请求完成后，调用 Web API **Dispose**在子作用域上。 使用**Dispose**方法来释放的控制器的依赖项。

如何实现**如何**取决于在 IoC 容器。 对于 Unity 中，作用域对应于在子容器：

[!code-csharp[Main](dependency-injection/samples/sample10.cs)]

大多数 IoC 容器具有类似的等效项。
