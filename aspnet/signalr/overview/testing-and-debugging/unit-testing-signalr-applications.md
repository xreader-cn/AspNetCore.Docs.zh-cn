---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: SignalR 应用程序单元测试 |Microsoft Docs
author: bradygaster
description: 本文介绍如何使用 SignalR 2.0 的单元测试功能。
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: cb4eb25aeedfe31ac2606de9fe7d280eb95ce2e6
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836033"
---
<a name="unit-testing-signalr-applications"></a><span data-ttu-id="18aff-103">单元测试 SignalR 应用程序</span><span class="sxs-lookup"><span data-stu-id="18aff-103">Unit Testing SignalR Applications</span></span>
====================
<span data-ttu-id="18aff-104">通过[Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="18aff-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="18aff-105">本文介绍如何使用 SignalR 2 的单元测试功能。</span><span class="sxs-lookup"><span data-stu-id="18aff-105">This article describes using the Unit Testing features of SignalR 2.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="18aff-106">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="18aff-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="18aff-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="18aff-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="18aff-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="18aff-108">.NET 4.5</span></span>
> - <span data-ttu-id="18aff-109">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="18aff-109">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="18aff-110">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="18aff-110">Questions and comments</span></span>
>
> <span data-ttu-id="18aff-111">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="18aff-111">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="18aff-112">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="18aff-112">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a><span data-ttu-id="18aff-113">SignalR 应用程序单元测试</span><span class="sxs-lookup"><span data-stu-id="18aff-113">Unit testing SignalR applications</span></span>

<span data-ttu-id="18aff-114">可以使用 SignalR 2 中的单元测试功能的 SignalR 应用程序创建单元测试。</span><span class="sxs-lookup"><span data-stu-id="18aff-114">You can use the unit test features in SignalR 2 to create unit tests for your SignalR application.</span></span> <span data-ttu-id="18aff-115">SignalR 2 包括[IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx)接口，可用于创建 mock 对象来模拟用于测试的集线器方法。</span><span class="sxs-lookup"><span data-stu-id="18aff-115">SignalR 2 includes the [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) interface, which can be used to create a mock object to simulate your hub methods for testing.</span></span>

<span data-ttu-id="18aff-116">在本部分中，你将添加的应用程序中创建单元测试[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)使用[XUnit.net](https://github.com/xunit/xunit)并[Moq](https://github.com/Moq/moq4)。</span><span class="sxs-lookup"><span data-stu-id="18aff-116">In this section, you'll add unit tests for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using [XUnit.net](https://github.com/xunit/xunit) and [Moq](https://github.com/Moq/moq4).</span></span>

<span data-ttu-id="18aff-117">XUnit.net 将用于控制测试;Moq 将用于创建[模拟](http://en.wikipedia.org/wiki/Mock_object)用于测试的对象。</span><span class="sxs-lookup"><span data-stu-id="18aff-117">XUnit.net will be used to control the test; Moq will be used to create a [mock](http://en.wikipedia.org/wiki/Mock_object) object for testing.</span></span> <span data-ttu-id="18aff-118">如果所需的; 可以使用其他模拟框架[Nsubstitute 这样](http://nsubstitute.github.io/)也是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="18aff-118">Other mocking frameworks can be used if desired; [NSubstitute](http://nsubstitute.github.io/) is also a good choice.</span></span> <span data-ttu-id="18aff-119">本教程演示如何设置的两种方式是模拟对象：首先，使用`dynamic`对象 （在.NET Framework 4 中引入） 与第二，使用的接口。</span><span class="sxs-lookup"><span data-stu-id="18aff-119">This tutorial demonstrates how to set up the mock object in two ways: First, using a `dynamic` object (introduced in .NET Framework 4), and second, using an interface.</span></span>

### <a name="contents"></a><span data-ttu-id="18aff-120">内容</span><span class="sxs-lookup"><span data-stu-id="18aff-120">Contents</span></span>

<span data-ttu-id="18aff-121">本教程包含以下各节。</span><span class="sxs-lookup"><span data-stu-id="18aff-121">This tutorial contains the following sections.</span></span>

- [<span data-ttu-id="18aff-122">使用动态进行单元测试</span><span class="sxs-lookup"><span data-stu-id="18aff-122">Unit testing with Dynamic</span></span>](#dynamic)
- [<span data-ttu-id="18aff-123">单元测试的类型</span><span class="sxs-lookup"><span data-stu-id="18aff-123">Unit testing by type</span></span>](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a><span data-ttu-id="18aff-124">使用动态进行单元测试</span><span class="sxs-lookup"><span data-stu-id="18aff-124">Unit testing with Dynamic</span></span>

<span data-ttu-id="18aff-125">在本部分中，您将添加在中创建的应用程序的单元测试[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)使用动态对象。</span><span class="sxs-lookup"><span data-stu-id="18aff-125">In this section, you'll add a unit test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using a dynamic object.</span></span>

1. <span data-ttu-id="18aff-126">安装[XUnit 运行程序扩展](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099)适用于 Visual Studio 2013。</span><span class="sxs-lookup"><span data-stu-id="18aff-126">Install the [XUnit Runner extension](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) for Visual Studio 2013.</span></span>
2. <span data-ttu-id="18aff-127">完成[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)，或下载完整的应用程序从[MSDN 代码库](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)。</span><span class="sxs-lookup"><span data-stu-id="18aff-127">Either complete the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md), or download the completed application from [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span></span>
3. <span data-ttu-id="18aff-128">如果使用快速入门应用程序的下载版本，打开**程序包管理器控制台**然后单击**还原**将 SignalR 程序包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="18aff-128">If you are using the download version of the Getting Started application, open **Package Manager Console** and click **Restore** to add the SignalR package to the project.</span></span>

    ![还原包](unit-testing-signalr-applications/_static/image1.png)
4. <span data-ttu-id="18aff-130">将项目添加到单元测试的解决方案。</span><span class="sxs-lookup"><span data-stu-id="18aff-130">Add a project to the solution for the unit test.</span></span> <span data-ttu-id="18aff-131">右键单击你的解决方案中**解决方案资源管理器**，然后选择**添加**，**新建项目...**.下**C#** 节点中，选择**Windows**节点。</span><span class="sxs-lookup"><span data-stu-id="18aff-131">Right-click your solution in **Solution Explorer** and select **Add**, **New Project...**. Under the **C#** node, select the **Windows** node.</span></span> <span data-ttu-id="18aff-132">选择**类库**。</span><span class="sxs-lookup"><span data-stu-id="18aff-132">Select **Class Library**.</span></span> <span data-ttu-id="18aff-133">将新项目命名**TestLibrary**然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="18aff-133">Name the new project **TestLibrary** and click **OK**.</span></span>

    ![创建测试库](unit-testing-signalr-applications/_static/image2.png)
5. <span data-ttu-id="18aff-135">到 SignalRChat 项目测试库项目中添加的引用。</span><span class="sxs-lookup"><span data-stu-id="18aff-135">Add a reference in the test library project to the SignalRChat project.</span></span> <span data-ttu-id="18aff-136">右键单击**TestLibrary**项目，然后选择**添加**，**引用...**.选择**项目**节点下的**解决方案**节点，并选中**SignalRChat**。</span><span class="sxs-lookup"><span data-stu-id="18aff-136">Right-click the **TestLibrary** project and select **Add**, **Reference...**. Select the **Projects** node under the **Solution** node, and check **SignalRChat**.</span></span> <span data-ttu-id="18aff-137">单击 **“确定”**。</span><span class="sxs-lookup"><span data-stu-id="18aff-137">Click **OK**.</span></span>

    ![添加项目引用](unit-testing-signalr-applications/_static/image3.png)
6. <span data-ttu-id="18aff-139">添加到包 SignalR、 Moq 和 XUnit **TestLibrary**项目。</span><span class="sxs-lookup"><span data-stu-id="18aff-139">Add the SignalR, Moq, and XUnit packages to the **TestLibrary** project.</span></span> <span data-ttu-id="18aff-140">在中**程序包管理器控制台**，请设置**默认项目**下拉列表中**TestLibrary**。</span><span class="sxs-lookup"><span data-stu-id="18aff-140">In the **Package Manager Console**, set the **Default Project** dropdown to **TestLibrary**.</span></span> <span data-ttu-id="18aff-141">在控制台窗口中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="18aff-141">Run the following commands in the console window:</span></span>

   - `Install-Package Microsoft.AspNet.SignalR`
   - `Install-Package Moq`
   - `Install-Package XUnit`

     ![安装包](unit-testing-signalr-applications/_static/image4.png)
7. <span data-ttu-id="18aff-143">创建测试文件。</span><span class="sxs-lookup"><span data-stu-id="18aff-143">Create the test file.</span></span> <span data-ttu-id="18aff-144">右键单击**TestLibrary**项目，然后单击**添加...**，**类**。</span><span class="sxs-lookup"><span data-stu-id="18aff-144">Right-click the **TestLibrary** project and click **Add...**, **Class**.</span></span> <span data-ttu-id="18aff-145">将新类命名**Tests.cs**。</span><span class="sxs-lookup"><span data-stu-id="18aff-145">Name the new class **Tests.cs**.</span></span>
8. <span data-ttu-id="18aff-146">将以下代码替换为 Tests.cs 的内容。</span><span class="sxs-lookup"><span data-stu-id="18aff-146">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    <span data-ttu-id="18aff-147">在上面的代码中，使用创建测试客户端`Mock`对象从[Moq](https://github.com/Moq/moq4)的类型库[IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (SignalR 2.1 中分配`dynamic`类型参数。）`IHubCallerConnectionContext`接口是用于调用客户端上的方法的代理对象。</span><span class="sxs-lookup"><span data-stu-id="18aff-147">In the code above, a test client is created using the `Mock` object from the [Moq](https://github.com/Moq/moq4) library, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span> <span data-ttu-id="18aff-148">`broadcastMessage`函数然后定义模拟客户端，以便它可以由调用`ChatHub`类。</span><span class="sxs-lookup"><span data-stu-id="18aff-148">The `broadcastMessage` function is then defined for the mock client so that it can be called by the `ChatHub` class.</span></span> <span data-ttu-id="18aff-149">测试引擎会调用`Send`方法`ChatHub`类，该类又会调用模拟`broadcastMessage`函数。</span><span class="sxs-lookup"><span data-stu-id="18aff-149">The test engine then calls the `Send` method of the `ChatHub` class, which in turn calls the mocked `broadcastMessage` function.</span></span>
9. <span data-ttu-id="18aff-150">生成解决方案，通过按**F6**。</span><span class="sxs-lookup"><span data-stu-id="18aff-150">Build the solution by pressing **F6**.</span></span>
10. <span data-ttu-id="18aff-151">运行单元测试。</span><span class="sxs-lookup"><span data-stu-id="18aff-151">Run the unit test.</span></span> <span data-ttu-id="18aff-152">在 Visual Studio 中，选择**测试**， **Windows**，**测试资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="18aff-152">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="18aff-153">在测试资源管理器窗口中，右键单击**HubsAreMockableViaDynamic** ，然后选择**运行选定测试**。</span><span class="sxs-lookup"><span data-stu-id="18aff-153">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![测试资源管理器](unit-testing-signalr-applications/_static/image5.png)
11. <span data-ttu-id="18aff-155">验证通过检查在测试资源管理器窗口的下部窗格已通过测试。</span><span class="sxs-lookup"><span data-stu-id="18aff-155">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="18aff-156">窗口将显示该测试已通过。</span><span class="sxs-lookup"><span data-stu-id="18aff-156">The window will show that the test passed.</span></span>

    ![已通过测试](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a><span data-ttu-id="18aff-158">单元测试的类型</span><span class="sxs-lookup"><span data-stu-id="18aff-158">Unit testing by type</span></span>

<span data-ttu-id="18aff-159">在本部分中，你将添加一个测试中创建的应用程序[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)使用的接口，包含要进行测试的方法。</span><span class="sxs-lookup"><span data-stu-id="18aff-159">In this section, you'll add a test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using an interface that contains the method to be tested.</span></span>

1. <span data-ttu-id="18aff-160">完成步骤 1-7[单元测试与动态](#dynamic)上述教程。</span><span class="sxs-lookup"><span data-stu-id="18aff-160">Complete steps 1-7 in the [Unit testing with Dynamic](#dynamic) tutorial above.</span></span>
2. <span data-ttu-id="18aff-161">将以下代码替换为 Tests.cs 的内容。</span><span class="sxs-lookup"><span data-stu-id="18aff-161">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    <span data-ttu-id="18aff-162">在上面的代码中，定义的签名创建接口`broadcastMessage`测试引擎将为其创建模拟客户端的方法。</span><span class="sxs-lookup"><span data-stu-id="18aff-162">In the code above, an interface is created defining the signature of the `broadcastMessage` method for which the test engine will create a mock client.</span></span> <span data-ttu-id="18aff-163">然后使用创建模拟客户端`Mock`类型的对象[IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (在 SignalR 2.1 中，将分配`dynamic`类型参数。)`IHubCallerConnectionContext`接口是用于调用客户端上的方法的代理对象。</span><span class="sxs-lookup"><span data-stu-id="18aff-163">A mock client is then created using the `Mock` object, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span>

    <span data-ttu-id="18aff-164">测试然后创建的实例`ChatHub`，然后创建 mock 版本的`broadcastMessage`方法，后者又调用通过调用`Send`集线器上的方法。</span><span class="sxs-lookup"><span data-stu-id="18aff-164">The test then creates an instance of `ChatHub`, and then creates a mock version of the `broadcastMessage` method, which in turn is invoked by calling the `Send` method on the hub.</span></span>
3. <span data-ttu-id="18aff-165">生成解决方案，通过按**F6**。</span><span class="sxs-lookup"><span data-stu-id="18aff-165">Build the solution by pressing **F6**.</span></span>
4. <span data-ttu-id="18aff-166">运行单元测试。</span><span class="sxs-lookup"><span data-stu-id="18aff-166">Run the unit test.</span></span> <span data-ttu-id="18aff-167">在 Visual Studio 中，选择**测试**， **Windows**，**测试资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="18aff-167">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="18aff-168">在测试资源管理器窗口中，右键单击**HubsAreMockableViaDynamic** ，然后选择**运行选定测试**。</span><span class="sxs-lookup"><span data-stu-id="18aff-168">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![测试资源管理器](unit-testing-signalr-applications/_static/image7.png)
5. <span data-ttu-id="18aff-170">验证通过检查在测试资源管理器窗口的下部窗格已通过测试。</span><span class="sxs-lookup"><span data-stu-id="18aff-170">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="18aff-171">窗口将显示该测试已通过。</span><span class="sxs-lookup"><span data-stu-id="18aff-171">The window will show that the test passed.</span></span>

    ![已通过测试](unit-testing-signalr-applications/_static/image8.png)
