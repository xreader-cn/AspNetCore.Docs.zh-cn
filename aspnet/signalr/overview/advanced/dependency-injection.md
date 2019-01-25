---
uid: signalr/overview/advanced/dependency-injection
title: 在 SignalR 中的依赖关系注入 |Microsoft Docs
author: bradygaster
description: Visual Studio 2013.NET 4.5 SignalR 本主题中使用软件版本信息有关早期版本的版本 2 的本主题的早期版本...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: a14121ae-02cf-4024-8af0-9dd0dc810690
msc.legacyurl: /signalr/overview/advanced/dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 54e263e277852d2d478ce5bccd4164254498831a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836605"
---
<a name="dependency-injection-in-signalr"></a>在 SignalR 中的依赖关系注入
====================
通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a>本主题中使用的软件版本
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR 版本 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>本主题的早期版本
>
> 有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。
>
> ## <a name="questions-and-comments"></a>问题和提出的意见
>
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。


依赖关系注入是一种方法来删除硬编码对象，使其更易替换对象的依赖项，无论是用于测试 （使用 mock 对象） 或运行时行为的更改之间的依赖项。 本教程演示如何执行 SignalR 集线器上的依赖关系注入。 它还演示如何使用 SignalR 使用 IoC 容器。 IoC 容器是依赖关系注入一个通用框架。

## <a name="what-is-dependency-injection"></a>什么是依赖关系注入？

如果你已了解如何使用依赖关系注入，跳过此部分。

*依赖关系注入*(DI) 是一种模式不负责创建其自己的依赖项对象。 下面是一个简单的示例以促使 DI。 假设有一个需要登录消息的对象。 您可以定义日志记录接口：

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

在您的对象，您可以创建`ILogger`记录消息：

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

此方法有效，但它不是最佳设计。 如果你想要替换`FileLogger`与另一个`ILogger`实现中，您将需要修改`SomeComponent`。 Supposing 大量其他对象使用`FileLogger`，将需要将所有这些更改。 或如果您决定将`FileLogger`单一实例，您还需要进行整个应用程序的更改。

更好的方法是"注入"`ILogger`到对象-例如，通过使用构造函数参数：

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

现在，该对象不负责选择的`ILogger`使用。 你可以从交换机`ILogger`而无需更改依赖于它的对象的实现。

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

此模式称为[构造函数注入](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection)。 另一种模式是 setter 注入，其中设置通过 setter 方法或属性的依赖关系。

## <a name="simple-dependency-injection-in-signalr"></a>在 SignalR 中的简单的依赖关系注入

本教程中的聊天应用程序，请考虑[SignalR 入门](../getting-started/tutorial-getting-started-with-signalr.md)。 下面是从该应用程序的中心类：

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

假设你想要发送之前存储在服务器上的聊天消息。 你可能会定义一个接口，用于提取此功能，并使用 DI 注入到接口`ChatHub`类。

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

唯一的问题是 SignalR 应用程序不会直接创建中心;SignalR 为你创建它们。 默认情况下，SignalR 希望 hub 类，具有无参数构造函数。 但是，可以轻松地注册创建集线器实例的函数，并使用此函数执行 DI。 通过调用注册函数**GlobalHost.DependencyResolver.Register**。

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

现在，SignalR 将调用此匿名函数，每当需要创建`ChatHub`实例。

## <a name="ioc-containers"></a>IoC 容器

上面的代码是相当不错的简单的用例。 但您仍需要编写如下：

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

在具有许多依赖关系的复杂应用程序，可能需要编写大量的此"绑定"代码。 此代码可能难以维护，尤其是如果嵌套依赖项。 也很难进行单元测试。

一种解决方案是使用 IoC 容器。 IoC 容器是一个软件组件，负责管理依赖项。您向容器注册类型，然后使用容器来创建对象。 容器自动找出的依赖关系。 多个 IoC 容器还可用于控制对象生存期和作用域等。

> [!NOTE]
> "IoC"代表"控制反转"，这是一个框架，其中调用到应用程序代码中的一般模式。 IoC 容器构造您的对象，其中"反转"常用控制流。


## <a name="using-ioc-containers-in-signalr"></a>在 SignalR 中使用 IoC 容器

聊天应用程序可能是非常简单，从 IoC 容器中受益。 相反，让我们看看[StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample)示例。

StockTicker 示例定义两个主要类：

- `StockTickerHub`：Hub 类，用于管理客户端连接。
- `StockTicker`：单一实例，它包含股票价格并定期更新它们。

`StockTickerHub` 保存对的引用`StockTicker`单一实例，而`StockTicker`持有一个指向引用**IHubConnectionContext**为`StockTickerHub`。 它使用此接口与通信`StockTickerHub`实例。 (有关详细信息，请参阅[服务器与 ASP.NET SignalR 广播](../getting-started/tutorial-server-broadcast-with-signalr.md)。)

我们可以使用 IoC 容器有点理清这些依赖关系。 首先，让我们来简化`StockTickerHub`和`StockTicker`类。 在下面的代码中，我已注释掉的部分，我们不需要。

删除无参数构造函数从`StockTickerHub`。 相反，我们将始终使用 DI 创建中心。

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

对于 StockTicker，删除的单一实例。 更高版本，我们将使用 IoC 容器来控制 StockTicker 生存期。 此外，请公共构造函数。

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

接下来，我们可以重构代码，通过创建一个接口，使`StockTicker`。 我们将使用此接口来分离`StockTickerHub`从`StockTicker`类。

Visual Studio 进行这种重构轻松。 打开文件 StockTicker.cs，右键单击`StockTicker`类声明，然后选择**重构**...**提取接口**。

![](dependency-injection/_static/image1.png)

在中**提取接口**对话框中，单击**全**。 保留其他默认值。 单击 **“确定”**。

![](dependency-injection/_static/image2.png)

Visual Studio 将创建名为的新接口`IStockTicker`，并且也会更改`StockTicker`为派生`IStockTicker`。

打开文件 IStockTicker.cs 并更改到接口**公共**。

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

在中`StockTickerHub`类中更改的两个实例`StockTicker`到`IStockTicker`:

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

创建`IStockTicker`接口并不是必需的但我将演示如何 DI 可以帮助减少在应用程序中的组件之间的耦合。

## <a name="add-the-ninject-library"></a>添加 Ninject 库

有许多适用于.NET 的开源 IoC 容器。 对于本教程中，我将使用[Ninject](http://www.ninject.org/)。 (其他常用库包括[Castle Windsor](http://www.castleproject.org/)， [Spring.Net](http://www.springframework.net/)， [Autofac](https://code.google.com/p/autofac/)， [Unity](https://github.com/unitycontainer/unity)，和[StructureMap](http://docs.structuremap.net).)

使用 NuGet 包管理器来安装[Ninject 库](https://nuget.org/packages/Ninject/3.0.1.10)。 在 Visual Studio 中，从**工具**菜单中选择**NuGet 包管理器** > **包管理器控制台**。 在包管理器控制台窗口中，输入以下命令：

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a>将 SignalR 依赖关系解析程序

若要使用 Ninject SignalR 中的，创建派生的类**DefaultDependencyResolver**。

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

此类重写**GetService**并**getservices 进行提取**方法**DefaultDependencyResolver**。 SignalR 调用这些方法在运行时，包括中心实例，以及由 SignalR 在内部使用的各种服务中创建各种对象。

- **GetService**方法创建一种类型的单个实例。 重写此方法以调用 Ninject 内核**TryGet**方法。 如果该方法将返回 null，回退到默认冲突解决程序。
- **Getservices 进行提取**方法创建指定类型的对象的集合。 重写此方法要串联的结果与默认冲突解决程序 Ninject 的结果。

## <a name="configure-ninject-bindings"></a>配置 Ninject 绑定

现在我们将使用 Ninject 声明类型绑定。

打开应用程序的 Startup.cs 类 (，您创建的或者手动根据中的包说明`readme.txt`，或通过将身份验证添加到你的项目创建)。 在中`Startup.Configuration`方法中，创建 Ninject 容器，它调用 Ninject*内核*。

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

创建我们的自定义依赖关系解析程序的实例：

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

为创建绑定`IStockTicker`，如下所示：

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

此代码会说两件事情。 首先，每当应用程序需要`IStockTicker`，内核应创建的实例`StockTicker`。 第二个，`StockTicker`类应创建为单一实例对象。 Ninject 将创建一个对象的实例，并返回每个请求的相同实例。

为创建绑定**IHubConnectionContext** ，如下所示：

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

一个匿名函数，返回此代码 creatres **IHubConnection**。 **WhenInjectedInto**方法会示意 Ninject 时要使用此函数仅创建`IStockTicker`实例。 原因是 SignalR 创建**IHubConnectionContext**实例在内部，并且我们不想要重写 SignalR 如何创建它们。 此函数仅适用于我们`StockTicker`类。

传递到依赖关系解析程序**MapSignalR**通过添加中心配置的方法：

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

使用新参数更新示例的 Startup 类中的 Startup.ConfigureSignalR 方法：

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

现在，SignalR 将使用冲突解决程序中指定**MapSignalR**，而不是默认冲突解决程序。

下面是完整代码列表`Startup.Configuration`。

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

若要在 Visual Studio 中运行 StockTicker 应用程序，按 F5。 在浏览器窗口中，导航到`http://localhost:*port*/SignalR.Sample/StockTicker.html`。

![](dependency-injection/_static/image3.png)

该应用程序功能与以前完全相同。 (有关说明，请参阅[服务器与 ASP.NET SignalR 广播](../getting-started/tutorial-server-broadcast-with-signalr.md)。)我们尚未更改的行为;只需进行更轻松地测试、 维护和改进代码。
