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
<a name="dependency-injection-in-signalr"></a><span data-ttu-id="49f69-103">在 SignalR 中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="49f69-103">Dependency Injection in SignalR</span></span>
====================
<span data-ttu-id="49f69-104">通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="49f69-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="49f69-105">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="49f69-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="49f69-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="49f69-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="49f69-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="49f69-107">.NET 4.5</span></span>
> - <span data-ttu-id="49f69-108">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="49f69-108">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="49f69-109">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="49f69-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="49f69-110">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="49f69-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="49f69-111">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="49f69-111">Questions and comments</span></span>
>
> <span data-ttu-id="49f69-112">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="49f69-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="49f69-113">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="49f69-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="49f69-114">依赖关系注入是一种方法来删除硬编码对象，使其更易替换对象的依赖项，无论是用于测试 （使用 mock 对象） 或运行时行为的更改之间的依赖项。</span><span class="sxs-lookup"><span data-stu-id="49f69-114">Dependency injection is a way to remove hard-coded dependencies between objects, making it easier to replace an object's dependencies, either for testing (using mock objects) or to change run-time behavior.</span></span> <span data-ttu-id="49f69-115">本教程演示如何执行 SignalR 集线器上的依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="49f69-115">This tutorial shows how to perform dependency injection on SignalR hubs.</span></span> <span data-ttu-id="49f69-116">它还演示如何使用 SignalR 使用 IoC 容器。</span><span class="sxs-lookup"><span data-stu-id="49f69-116">It also shows how to use IoC containers with SignalR.</span></span> <span data-ttu-id="49f69-117">IoC 容器是依赖关系注入一个通用框架。</span><span class="sxs-lookup"><span data-stu-id="49f69-117">An IoC container is a general framework for dependency injection.</span></span>

## <a name="what-is-dependency-injection"></a><span data-ttu-id="49f69-118">什么是依赖关系注入？</span><span class="sxs-lookup"><span data-stu-id="49f69-118">What is Dependency Injection?</span></span>

<span data-ttu-id="49f69-119">如果你已了解如何使用依赖关系注入，跳过此部分。</span><span class="sxs-lookup"><span data-stu-id="49f69-119">Skip this section if you are already familiar with dependency injection.</span></span>

<span data-ttu-id="49f69-120">*依赖关系注入*(DI) 是一种模式不负责创建其自己的依赖项对象。</span><span class="sxs-lookup"><span data-stu-id="49f69-120">*Dependency injection* (DI) is a pattern where objects are not responsible for creating their own dependencies.</span></span> <span data-ttu-id="49f69-121">下面是一个简单的示例以促使 DI。</span><span class="sxs-lookup"><span data-stu-id="49f69-121">Here is a simple example to motivate DI.</span></span> <span data-ttu-id="49f69-122">假设有一个需要登录消息的对象。</span><span class="sxs-lookup"><span data-stu-id="49f69-122">Suppose you have an object that needs to log messages.</span></span> <span data-ttu-id="49f69-123">您可以定义日志记录接口：</span><span class="sxs-lookup"><span data-stu-id="49f69-123">You might define a logging interface:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

<span data-ttu-id="49f69-124">在您的对象，您可以创建`ILogger`记录消息：</span><span class="sxs-lookup"><span data-stu-id="49f69-124">In your object, you can create an `ILogger` to log messages:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

<span data-ttu-id="49f69-125">此方法有效，但它不是最佳设计。</span><span class="sxs-lookup"><span data-stu-id="49f69-125">This works, but it's not the best design.</span></span> <span data-ttu-id="49f69-126">如果你想要替换`FileLogger`与另一个`ILogger`实现中，您将需要修改`SomeComponent`。</span><span class="sxs-lookup"><span data-stu-id="49f69-126">If you want to replace `FileLogger` with another `ILogger` implementation, you will have to modify `SomeComponent`.</span></span> <span data-ttu-id="49f69-127">Supposing 大量其他对象使用`FileLogger`，将需要将所有这些更改。</span><span class="sxs-lookup"><span data-stu-id="49f69-127">Supposing that a lot of other objects use `FileLogger`, you will need to change all of them.</span></span> <span data-ttu-id="49f69-128">或如果您决定将`FileLogger`单一实例，您还需要进行整个应用程序的更改。</span><span class="sxs-lookup"><span data-stu-id="49f69-128">Or if you decide to make `FileLogger` a singleton, you'll also need to make changes throughout the application.</span></span>

<span data-ttu-id="49f69-129">更好的方法是"注入"`ILogger`到对象-例如，通过使用构造函数参数：</span><span class="sxs-lookup"><span data-stu-id="49f69-129">A better approach is to "inject" an `ILogger` into the object—for example, by using a constructor argument:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

<span data-ttu-id="49f69-130">现在，该对象不负责选择的`ILogger`使用。</span><span class="sxs-lookup"><span data-stu-id="49f69-130">Now the object is not responsible for selecting which `ILogger` to use.</span></span> <span data-ttu-id="49f69-131">你可以从交换机`ILogger`而无需更改依赖于它的对象的实现。</span><span class="sxs-lookup"><span data-stu-id="49f69-131">You can swich `ILogger` implementations without changing the objects that depend on it.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

<span data-ttu-id="49f69-132">此模式称为[构造函数注入](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection)。</span><span class="sxs-lookup"><span data-stu-id="49f69-132">This pattern is called [constructor injection](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection).</span></span> <span data-ttu-id="49f69-133">另一种模式是 setter 注入，其中设置通过 setter 方法或属性的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="49f69-133">Another pattern is setter injection, where you set the dependency through a setter method or property.</span></span>

## <a name="simple-dependency-injection-in-signalr"></a><span data-ttu-id="49f69-134">在 SignalR 中的简单的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="49f69-134">Simple Dependency Injection in SignalR</span></span>

<span data-ttu-id="49f69-135">本教程中的聊天应用程序，请考虑[SignalR 入门](../getting-started/tutorial-getting-started-with-signalr.md)。</span><span class="sxs-lookup"><span data-stu-id="49f69-135">Consider the Chat application from the tutorial [Getting Started with SignalR](../getting-started/tutorial-getting-started-with-signalr.md).</span></span> <span data-ttu-id="49f69-136">下面是从该应用程序的中心类：</span><span class="sxs-lookup"><span data-stu-id="49f69-136">Here is the hub class from that application:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

<span data-ttu-id="49f69-137">假设你想要发送之前存储在服务器上的聊天消息。</span><span class="sxs-lookup"><span data-stu-id="49f69-137">Suppose that you want to store chat messages on the server before sending them.</span></span> <span data-ttu-id="49f69-138">你可能会定义一个接口，用于提取此功能，并使用 DI 注入到接口`ChatHub`类。</span><span class="sxs-lookup"><span data-stu-id="49f69-138">You might define an interface that abstracts this functionality, and use DI to inject the interface into the `ChatHub` class.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

<span data-ttu-id="49f69-139">唯一的问题是 SignalR 应用程序不会直接创建中心;SignalR 为你创建它们。</span><span class="sxs-lookup"><span data-stu-id="49f69-139">The only problem is that a SignalR application does not directly create hubs; SignalR creates them for you.</span></span> <span data-ttu-id="49f69-140">默认情况下，SignalR 希望 hub 类，具有无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="49f69-140">By default, SignalR expects a hub class to have a parameterless constructor.</span></span> <span data-ttu-id="49f69-141">但是，可以轻松地注册创建集线器实例的函数，并使用此函数执行 DI。</span><span class="sxs-lookup"><span data-stu-id="49f69-141">However, you can easily register a function to create hub instances, and use this function to perform DI.</span></span> <span data-ttu-id="49f69-142">通过调用注册函数**GlobalHost.DependencyResolver.Register**。</span><span class="sxs-lookup"><span data-stu-id="49f69-142">Register the function by calling **GlobalHost.DependencyResolver.Register**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample7.cs)]

<span data-ttu-id="49f69-143">现在，SignalR 将调用此匿名函数，每当需要创建`ChatHub`实例。</span><span class="sxs-lookup"><span data-stu-id="49f69-143">Now SignalR will invoke this anonymous function whenever it needs to create a `ChatHub` instance.</span></span>

## <a name="ioc-containers"></a><span data-ttu-id="49f69-144">IoC 容器</span><span class="sxs-lookup"><span data-stu-id="49f69-144">IoC Containers</span></span>

<span data-ttu-id="49f69-145">上面的代码是相当不错的简单的用例。</span><span class="sxs-lookup"><span data-stu-id="49f69-145">The previous code is fine for simple cases.</span></span> <span data-ttu-id="49f69-146">但您仍需要编写如下：</span><span class="sxs-lookup"><span data-stu-id="49f69-146">But you still had to write this:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

<span data-ttu-id="49f69-147">在具有许多依赖关系的复杂应用程序，可能需要编写大量的此"绑定"代码。</span><span class="sxs-lookup"><span data-stu-id="49f69-147">In a complex application with many dependencies, you might need to write a lot of this "wiring" code.</span></span> <span data-ttu-id="49f69-148">此代码可能难以维护，尤其是如果嵌套依赖项。</span><span class="sxs-lookup"><span data-stu-id="49f69-148">This code can be hard to maintain, especially if dependencies are nested.</span></span> <span data-ttu-id="49f69-149">也很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="49f69-149">It is also hard to unit test.</span></span>

<span data-ttu-id="49f69-150">一种解决方案是使用 IoC 容器。</span><span class="sxs-lookup"><span data-stu-id="49f69-150">One solution is to use an IoC container.</span></span> <span data-ttu-id="49f69-151">IoC 容器是一个软件组件，负责管理依赖项。您向容器注册类型，然后使用容器来创建对象。</span><span class="sxs-lookup"><span data-stu-id="49f69-151">An IoC container is a software component that is responsible for managing dependencies.You register types with the container, and then use the container to create objects.</span></span> <span data-ttu-id="49f69-152">容器自动找出的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="49f69-152">The container automatically figures out the dependency relations.</span></span> <span data-ttu-id="49f69-153">多个 IoC 容器还可用于控制对象生存期和作用域等。</span><span class="sxs-lookup"><span data-stu-id="49f69-153">Many IoC containers also allow you to control things like object lifetime and scope.</span></span>

> [!NOTE]
> <span data-ttu-id="49f69-154">"IoC"代表"控制反转"，这是一个框架，其中调用到应用程序代码中的一般模式。</span><span class="sxs-lookup"><span data-stu-id="49f69-154">"IoC" stands for "inversion of control", which is a general pattern where a framework calls into application code.</span></span> <span data-ttu-id="49f69-155">IoC 容器构造您的对象，其中"反转"常用控制流。</span><span class="sxs-lookup"><span data-stu-id="49f69-155">An IoC container constructs your objects for you, which "inverts" the usual flow of control.</span></span>


## <a name="using-ioc-containers-in-signalr"></a><span data-ttu-id="49f69-156">在 SignalR 中使用 IoC 容器</span><span class="sxs-lookup"><span data-stu-id="49f69-156">Using IoC Containers in SignalR</span></span>

<span data-ttu-id="49f69-157">聊天应用程序可能是非常简单，从 IoC 容器中受益。</span><span class="sxs-lookup"><span data-stu-id="49f69-157">The Chat application is probably too simple to benefit from an IoC container.</span></span> <span data-ttu-id="49f69-158">相反，让我们看看[StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample)示例。</span><span class="sxs-lookup"><span data-stu-id="49f69-158">Instead, let's look at the [StockTicker](http://nuget.org/packages/microsoft.aspnet.signalr.sample) sample.</span></span>

<span data-ttu-id="49f69-159">StockTicker 示例定义两个主要类：</span><span class="sxs-lookup"><span data-stu-id="49f69-159">The StockTicker sample defines two main classes:</span></span>

- <span data-ttu-id="49f69-160">`StockTickerHub`：Hub 类，用于管理客户端连接。</span><span class="sxs-lookup"><span data-stu-id="49f69-160">`StockTickerHub`: The hub class, which manages client connections.</span></span>
- <span data-ttu-id="49f69-161">`StockTicker`：单一实例，它包含股票价格并定期更新它们。</span><span class="sxs-lookup"><span data-stu-id="49f69-161">`StockTicker`: A singleton that holds stock prices and periodically updates them.</span></span>

<span data-ttu-id="49f69-162">`StockTickerHub` 保存对的引用`StockTicker`单一实例，而`StockTicker`持有一个指向引用**IHubConnectionContext**为`StockTickerHub`。</span><span class="sxs-lookup"><span data-stu-id="49f69-162">`StockTickerHub` holds a reference to the `StockTicker` singleton, while `StockTicker` holds a reference to the **IHubConnectionContext** for the `StockTickerHub`.</span></span> <span data-ttu-id="49f69-163">它使用此接口与通信`StockTickerHub`实例。</span><span class="sxs-lookup"><span data-stu-id="49f69-163">It uses this interface to communicate with `StockTickerHub` instances.</span></span> <span data-ttu-id="49f69-164">(有关详细信息，请参阅[服务器与 ASP.NET SignalR 广播](../getting-started/tutorial-server-broadcast-with-signalr.md)。)</span><span class="sxs-lookup"><span data-stu-id="49f69-164">(For more information, see [Server Broadcast with ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).)</span></span>

<span data-ttu-id="49f69-165">我们可以使用 IoC 容器有点理清这些依赖关系。</span><span class="sxs-lookup"><span data-stu-id="49f69-165">We can use an IoC container to untangle these dependencies a bit.</span></span> <span data-ttu-id="49f69-166">首先，让我们来简化`StockTickerHub`和`StockTicker`类。</span><span class="sxs-lookup"><span data-stu-id="49f69-166">First, let's simplify the `StockTickerHub` and `StockTicker` classes.</span></span> <span data-ttu-id="49f69-167">在下面的代码中，我已注释掉的部分，我们不需要。</span><span class="sxs-lookup"><span data-stu-id="49f69-167">In the following code, I've commented out the parts that we don't need.</span></span>

<span data-ttu-id="49f69-168">删除无参数构造函数从`StockTickerHub`。</span><span class="sxs-lookup"><span data-stu-id="49f69-168">Remove the parameterless constructor from `StockTickerHub`.</span></span> <span data-ttu-id="49f69-169">相反，我们将始终使用 DI 创建中心。</span><span class="sxs-lookup"><span data-stu-id="49f69-169">Instead, we will always use DI to create the hub.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

<span data-ttu-id="49f69-170">对于 StockTicker，删除的单一实例。</span><span class="sxs-lookup"><span data-stu-id="49f69-170">For StockTicker, remove the singleton instance.</span></span> <span data-ttu-id="49f69-171">更高版本，我们将使用 IoC 容器来控制 StockTicker 生存期。</span><span class="sxs-lookup"><span data-stu-id="49f69-171">Later, we'll use the IoC container to control the StockTicker lifetime.</span></span> <span data-ttu-id="49f69-172">此外，请公共构造函数。</span><span class="sxs-lookup"><span data-stu-id="49f69-172">Also, make the constructor public.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample10.cs?highlight=7)]

<span data-ttu-id="49f69-173">接下来，我们可以重构代码，通过创建一个接口，使`StockTicker`。</span><span class="sxs-lookup"><span data-stu-id="49f69-173">Next, we can refactor the code by creating an interface for `StockTicker`.</span></span> <span data-ttu-id="49f69-174">我们将使用此接口来分离`StockTickerHub`从`StockTicker`类。</span><span class="sxs-lookup"><span data-stu-id="49f69-174">We'll use this interface to decouple the `StockTickerHub` from the `StockTicker` class.</span></span>

<span data-ttu-id="49f69-175">Visual Studio 进行这种重构轻松。</span><span class="sxs-lookup"><span data-stu-id="49f69-175">Visual Studio makes this kind of refactoring easy.</span></span> <span data-ttu-id="49f69-176">打开文件 StockTicker.cs，右键单击`StockTicker`类声明，然后选择**重构**...**提取接口**。</span><span class="sxs-lookup"><span data-stu-id="49f69-176">Open the file StockTicker.cs, right-click on the `StockTicker` class declaration, and select **Refactor** ... **Extract Interface**.</span></span>

![](dependency-injection/_static/image1.png)

<span data-ttu-id="49f69-177">在中**提取接口**对话框中，单击**全**。</span><span class="sxs-lookup"><span data-stu-id="49f69-177">In the **Extract Interface** dialog, click **Select All**.</span></span> <span data-ttu-id="49f69-178">保留其他默认值。</span><span class="sxs-lookup"><span data-stu-id="49f69-178">Leave the other defaults.</span></span> <span data-ttu-id="49f69-179">单击 **“确定”**。</span><span class="sxs-lookup"><span data-stu-id="49f69-179">Click **OK**.</span></span>

![](dependency-injection/_static/image2.png)

<span data-ttu-id="49f69-180">Visual Studio 将创建名为的新接口`IStockTicker`，并且也会更改`StockTicker`为派生`IStockTicker`。</span><span class="sxs-lookup"><span data-stu-id="49f69-180">Visual Studio creates a new interface named `IStockTicker`, and also changes `StockTicker` to derive from `IStockTicker`.</span></span>

<span data-ttu-id="49f69-181">打开文件 IStockTicker.cs 并更改到接口**公共**。</span><span class="sxs-lookup"><span data-stu-id="49f69-181">Open the file IStockTicker.cs and change the interface to **public**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample11.cs?highlight=1)]

<span data-ttu-id="49f69-182">在中`StockTickerHub`类中更改的两个实例`StockTicker`到`IStockTicker`:</span><span class="sxs-lookup"><span data-stu-id="49f69-182">In the `StockTickerHub` class, change the two instances of `StockTicker` to `IStockTicker`:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample12.cs?highlight=4,6)]

<span data-ttu-id="49f69-183">创建`IStockTicker`接口并不是必需的但我将演示如何 DI 可以帮助减少在应用程序中的组件之间的耦合。</span><span class="sxs-lookup"><span data-stu-id="49f69-183">Creating an `IStockTicker` interface isn't strictly necessary, but I wanted to show how DI can help to reduce coupling between components in your application.</span></span>

## <a name="add-the-ninject-library"></a><span data-ttu-id="49f69-184">添加 Ninject 库</span><span class="sxs-lookup"><span data-stu-id="49f69-184">Add the Ninject Library</span></span>

<span data-ttu-id="49f69-185">有许多适用于.NET 的开源 IoC 容器。</span><span class="sxs-lookup"><span data-stu-id="49f69-185">There are many open-source IoC containers for .NET.</span></span> <span data-ttu-id="49f69-186">对于本教程中，我将使用[Ninject](http://www.ninject.org/)。</span><span class="sxs-lookup"><span data-stu-id="49f69-186">For this tutorial, I'll use [Ninject](http://www.ninject.org/).</span></span> <span data-ttu-id="49f69-187">(其他常用库包括[Castle Windsor](http://www.castleproject.org/)， [Spring.Net](http://www.springframework.net/)， [Autofac](https://code.google.com/p/autofac/)， [Unity](https://github.com/unitycontainer/unity)，和[StructureMap](http://docs.structuremap.net).)</span><span class="sxs-lookup"><span data-stu-id="49f69-187">(Other popular libraries include [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Unity](https://github.com/unitycontainer/unity), and [StructureMap](http://docs.structuremap.net).)</span></span>

<span data-ttu-id="49f69-188">使用 NuGet 包管理器来安装[Ninject 库](https://nuget.org/packages/Ninject/3.0.1.10)。</span><span class="sxs-lookup"><span data-stu-id="49f69-188">Use NuGet Package Manager to install the [Ninject library](https://nuget.org/packages/Ninject/3.0.1.10).</span></span> <span data-ttu-id="49f69-189">在 Visual Studio 中，从**工具**菜单中选择**NuGet 包管理器** > **包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="49f69-189">In Visual Studio, from the **Tools** menu select **NuGet Package Manager** > **Package Manager Console**.</span></span> <span data-ttu-id="49f69-190">在包管理器控制台窗口中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="49f69-190">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](dependency-injection/samples/sample13.ps1)]

## <a name="replace-the-signalr-dependency-resolver"></a><span data-ttu-id="49f69-191">将 SignalR 依赖关系解析程序</span><span class="sxs-lookup"><span data-stu-id="49f69-191">Replace the SignalR Dependency Resolver</span></span>

<span data-ttu-id="49f69-192">若要使用 Ninject SignalR 中的，创建派生的类**DefaultDependencyResolver**。</span><span class="sxs-lookup"><span data-stu-id="49f69-192">To use Ninject within SignalR, create a class that derives from **DefaultDependencyResolver**.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample14.cs)]

<span data-ttu-id="49f69-193">此类重写**GetService**并**getservices 进行提取**方法**DefaultDependencyResolver**。</span><span class="sxs-lookup"><span data-stu-id="49f69-193">This class overrides the **GetService** and **GetServices** methods of **DefaultDependencyResolver**.</span></span> <span data-ttu-id="49f69-194">SignalR 调用这些方法在运行时，包括中心实例，以及由 SignalR 在内部使用的各种服务中创建各种对象。</span><span class="sxs-lookup"><span data-stu-id="49f69-194">SignalR calls these methods to create various objects at runtime, including hub instances, as well as various services used internally by SignalR.</span></span>

- <span data-ttu-id="49f69-195">**GetService**方法创建一种类型的单个实例。</span><span class="sxs-lookup"><span data-stu-id="49f69-195">The **GetService** method creates a single instance of a type.</span></span> <span data-ttu-id="49f69-196">重写此方法以调用 Ninject 内核**TryGet**方法。</span><span class="sxs-lookup"><span data-stu-id="49f69-196">Override this method to call the Ninject kernel's **TryGet** method.</span></span> <span data-ttu-id="49f69-197">如果该方法将返回 null，回退到默认冲突解决程序。</span><span class="sxs-lookup"><span data-stu-id="49f69-197">If that method returns null, fall back to the default resolver.</span></span>
- <span data-ttu-id="49f69-198">**Getservices 进行提取**方法创建指定类型的对象的集合。</span><span class="sxs-lookup"><span data-stu-id="49f69-198">The **GetServices** method creates a collection of objects of a specified type.</span></span> <span data-ttu-id="49f69-199">重写此方法要串联的结果与默认冲突解决程序 Ninject 的结果。</span><span class="sxs-lookup"><span data-stu-id="49f69-199">Override this method to concatenate the results from Ninject with the results from the default resolver.</span></span>

## <a name="configure-ninject-bindings"></a><span data-ttu-id="49f69-200">配置 Ninject 绑定</span><span class="sxs-lookup"><span data-stu-id="49f69-200">Configure Ninject Bindings</span></span>

<span data-ttu-id="49f69-201">现在我们将使用 Ninject 声明类型绑定。</span><span class="sxs-lookup"><span data-stu-id="49f69-201">Now we'll use Ninject to declare type bindings.</span></span>

<span data-ttu-id="49f69-202">打开应用程序的 Startup.cs 类 (，您创建的或者手动根据中的包说明`readme.txt`，或通过将身份验证添加到你的项目创建)。</span><span class="sxs-lookup"><span data-stu-id="49f69-202">Open your application's Startup.cs class (that you either created manually as per the package instructions in `readme.txt`, or that was created by adding authentication to your project).</span></span> <span data-ttu-id="49f69-203">在中`Startup.Configuration`方法中，创建 Ninject 容器，它调用 Ninject*内核*。</span><span class="sxs-lookup"><span data-stu-id="49f69-203">In the `Startup.Configuration` method, create the Ninject container, which Ninject calls the *kernel*.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample15.cs)]

<span data-ttu-id="49f69-204">创建我们的自定义依赖关系解析程序的实例：</span><span class="sxs-lookup"><span data-stu-id="49f69-204">Create an instance of our custom dependency resolver:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample16.cs)]

<span data-ttu-id="49f69-205">为创建绑定`IStockTicker`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="49f69-205">Create a binding for `IStockTicker` as follows:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample17.cs)]

<span data-ttu-id="49f69-206">此代码会说两件事情。</span><span class="sxs-lookup"><span data-stu-id="49f69-206">This code is saying two things.</span></span> <span data-ttu-id="49f69-207">首先，每当应用程序需要`IStockTicker`，内核应创建的实例`StockTicker`。</span><span class="sxs-lookup"><span data-stu-id="49f69-207">First, whenever the application needs an `IStockTicker`, the kernel should create an instance of `StockTicker`.</span></span> <span data-ttu-id="49f69-208">第二个，`StockTicker`类应创建为单一实例对象。</span><span class="sxs-lookup"><span data-stu-id="49f69-208">Second, the `StockTicker` class should be a created as a singleton object.</span></span> <span data-ttu-id="49f69-209">Ninject 将创建一个对象的实例，并返回每个请求的相同实例。</span><span class="sxs-lookup"><span data-stu-id="49f69-209">Ninject will create one instance of the object, and return the same instance for each request.</span></span>

<span data-ttu-id="49f69-210">为创建绑定**IHubConnectionContext** ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="49f69-210">Create a binding for **IHubConnectionContext** as follows:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample18.cs)]

<span data-ttu-id="49f69-211">一个匿名函数，返回此代码 creatres **IHubConnection**。</span><span class="sxs-lookup"><span data-stu-id="49f69-211">This code creatres an anonymous function that returns an **IHubConnection**.</span></span> <span data-ttu-id="49f69-212">**WhenInjectedInto**方法会示意 Ninject 时要使用此函数仅创建`IStockTicker`实例。</span><span class="sxs-lookup"><span data-stu-id="49f69-212">The **WhenInjectedInto** method tells Ninject to use this function only when creating `IStockTicker` instances.</span></span> <span data-ttu-id="49f69-213">原因是 SignalR 创建**IHubConnectionContext**实例在内部，并且我们不想要重写 SignalR 如何创建它们。</span><span class="sxs-lookup"><span data-stu-id="49f69-213">The reason is that SignalR creates **IHubConnectionContext** instances internally, and we don't want to override how SignalR creates them.</span></span> <span data-ttu-id="49f69-214">此函数仅适用于我们`StockTicker`类。</span><span class="sxs-lookup"><span data-stu-id="49f69-214">This function only applies to our `StockTicker` class.</span></span>

<span data-ttu-id="49f69-215">传递到依赖关系解析程序**MapSignalR**通过添加中心配置的方法：</span><span class="sxs-lookup"><span data-stu-id="49f69-215">Pass the dependency resolver into the **MapSignalR** method by adding a hub configuration:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample19.cs)]

<span data-ttu-id="49f69-216">使用新参数更新示例的 Startup 类中的 Startup.ConfigureSignalR 方法：</span><span class="sxs-lookup"><span data-stu-id="49f69-216">Update the Startup.ConfigureSignalR method in the sample's Startup class with the new parameter:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample20.cs)]

<span data-ttu-id="49f69-217">现在，SignalR 将使用冲突解决程序中指定**MapSignalR**，而不是默认冲突解决程序。</span><span class="sxs-lookup"><span data-stu-id="49f69-217">Now SignalR will use the resolver specified in **MapSignalR**, instead of the default resolver.</span></span>

<span data-ttu-id="49f69-218">下面是完整代码列表`Startup.Configuration`。</span><span class="sxs-lookup"><span data-stu-id="49f69-218">Here is the complete code listing for `Startup.Configuration`.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample21.cs)]

<span data-ttu-id="49f69-219">若要在 Visual Studio 中运行 StockTicker 应用程序，按 F5。</span><span class="sxs-lookup"><span data-stu-id="49f69-219">To run the StockTicker application in Visual Studio, press F5.</span></span> <span data-ttu-id="49f69-220">在浏览器窗口中，导航到`http://localhost:*port*/SignalR.Sample/StockTicker.html`。</span><span class="sxs-lookup"><span data-stu-id="49f69-220">In the browser window, navigate to `http://localhost:*port*/SignalR.Sample/StockTicker.html`.</span></span>

![](dependency-injection/_static/image3.png)

<span data-ttu-id="49f69-221">该应用程序功能与以前完全相同。</span><span class="sxs-lookup"><span data-stu-id="49f69-221">The application has exactly the same functionality as before.</span></span> <span data-ttu-id="49f69-222">(有关说明，请参阅[服务器与 ASP.NET SignalR 广播](../getting-started/tutorial-server-broadcast-with-signalr.md)。)我们尚未更改的行为;只需进行更轻松地测试、 维护和改进代码。</span><span class="sxs-lookup"><span data-stu-id="49f69-222">(For a description, see [Server Broadcast with ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).) We haven't changed the behavior; just made the code easier to test, maintain, and evolve.</span></span>
