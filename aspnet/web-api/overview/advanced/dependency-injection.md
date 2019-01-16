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
<a name="dependency-injection-in-aspnet-web-api-2"></a><span data-ttu-id="421f7-104">ASP.NET Web API 2 中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="421f7-104">Dependency Injection in ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="421f7-105">通过[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="421f7-105">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="421f7-106">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="421f7-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/ASP-NET-Web-API-Tutorial-468ee148)

> <span data-ttu-id="421f7-107">本教程演示如何将依赖项注入到你的 ASP.NET Web API 控制器。</span><span class="sxs-lookup"><span data-stu-id="421f7-107">This tutorial shows how to inject dependencies into your ASP.NET Web API controller.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="421f7-108">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="421f7-108">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="421f7-109">Web API 2</span><span class="sxs-lookup"><span data-stu-id="421f7-109">Web API 2</span></span>
> - [<span data-ttu-id="421f7-110">Unity 应用程序块</span><span class="sxs-lookup"><span data-stu-id="421f7-110">Unity Application Block</span></span>](https://www.nuget.org/packages/Unity/)
> - <span data-ttu-id="421f7-111">Entity Framework 6 （也有效，版本 5）</span><span class="sxs-lookup"><span data-stu-id="421f7-111">Entity Framework 6 (version 5 also works)</span></span>


## <a name="what-is-dependency-injection"></a><span data-ttu-id="421f7-112">什么是依赖关系注入？</span><span class="sxs-lookup"><span data-stu-id="421f7-112">What is Dependency Injection?</span></span>

<span data-ttu-id="421f7-113">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="421f7-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="421f7-114">例如，是常见定义[存储库](http://martinfowler.com/eaaCatalog/repository.html)用于处理数据访问。</span><span class="sxs-lookup"><span data-stu-id="421f7-114">For example, it's common to define a [repository](http://martinfowler.com/eaaCatalog/repository.html) that handles data access.</span></span> <span data-ttu-id="421f7-115">让我们演示了通过一个示例。</span><span class="sxs-lookup"><span data-stu-id="421f7-115">Let's illustrate with an example.</span></span> <span data-ttu-id="421f7-116">首先，我们需要定义一个域模型：</span><span class="sxs-lookup"><span data-stu-id="421f7-116">First, we'll define a domain model:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample1.cs)]

<span data-ttu-id="421f7-117">下面是将项存储在数据库中，使用实体框架的简单存储库类。</span><span class="sxs-lookup"><span data-stu-id="421f7-117">Here is a simple repository class that stores items in a database, using Entity Framework.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample2.cs)]

<span data-ttu-id="421f7-118">现在让我们来定义支持 GET 请求的 Web API 控制器`Product`实体。</span><span class="sxs-lookup"><span data-stu-id="421f7-118">Now let's define a Web API controller that supports GET requests for `Product` entities.</span></span> <span data-ttu-id="421f7-119">（我已经将文章和其他方法为简单起见。）下面是第一次尝试：</span><span class="sxs-lookup"><span data-stu-id="421f7-119">(I'm leaving out POST and other methods for simplicity.) Here is a first attempt:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample3.cs)]

<span data-ttu-id="421f7-120">请注意，控制器类取决于`ProductRepository`，我们要让创建控制器和`ProductRepository`实例。</span><span class="sxs-lookup"><span data-stu-id="421f7-120">Notice that the controller class depends on `ProductRepository`, and we are letting the controller create the `ProductRepository` instance.</span></span> <span data-ttu-id="421f7-121">但是，它的原因是这种方式，依赖关系进行硬编码不是好方法。</span><span class="sxs-lookup"><span data-stu-id="421f7-121">However, it's a bad idea to hard code the dependency in this way, for several reasons.</span></span>

- <span data-ttu-id="421f7-122">如果你想要替换`ProductRepository`使用不同的实现，您还需要修改控制器类。</span><span class="sxs-lookup"><span data-stu-id="421f7-122">If you want to replace `ProductRepository` with a different implementation, you also need to modify the controller class.</span></span>
- <span data-ttu-id="421f7-123">如果`ProductRepository`具有依赖关系，您必须配置这些控制器中。</span><span class="sxs-lookup"><span data-stu-id="421f7-123">If the `ProductRepository` has dependencies, you must configure these inside the controller.</span></span> <span data-ttu-id="421f7-124">对于具有多个控制器的大型项目，配置代码变得分散于整个项目。</span><span class="sxs-lookup"><span data-stu-id="421f7-124">For a large project with multiple controllers, your configuration code becomes scattered across your project.</span></span>
- <span data-ttu-id="421f7-125">很难进行单元测试，因为控制器是硬编码查询数据库。</span><span class="sxs-lookup"><span data-stu-id="421f7-125">It is hard to unit test, because the controller is hard-coded to query the database.</span></span> <span data-ttu-id="421f7-126">对于单元测试时，应使用的 mock 或存根 （stub） 存储库，这是不可能成为当前设计。</span><span class="sxs-lookup"><span data-stu-id="421f7-126">For a unit test, you should use a mock or stub repository, which is not possible with the currect design.</span></span>

<span data-ttu-id="421f7-127">我们可以解决这些问题由*注入*到控制器的存储库。</span><span class="sxs-lookup"><span data-stu-id="421f7-127">We can address these problems by *injecting* the repository into the controller.</span></span> <span data-ttu-id="421f7-128">首先，重构`ProductRepository`到接口的类：</span><span class="sxs-lookup"><span data-stu-id="421f7-128">First, refactor the `ProductRepository` class into an interface:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample4.cs)]

<span data-ttu-id="421f7-129">然后提供`IProductRepository`作为构造函数参数：</span><span class="sxs-lookup"><span data-stu-id="421f7-129">Then provide the `IProductRepository` as a constructor parameter:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample5.cs)]

<span data-ttu-id="421f7-130">此示例使用[构造函数注入](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection)。</span><span class="sxs-lookup"><span data-stu-id="421f7-130">This example uses [constructor injection](http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection).</span></span> <span data-ttu-id="421f7-131">此外可以使用*setter 注入*，设置通过 setter 方法或属性的依赖项的位置。</span><span class="sxs-lookup"><span data-stu-id="421f7-131">You can also use *setter injection*, where you set the dependency through a setter method or property.</span></span>

<span data-ttu-id="421f7-132">但现在没有问题，因为你的应用程序不会直接创建控制器。</span><span class="sxs-lookup"><span data-stu-id="421f7-132">But now there is a problem, because your application doesn't create the controller directly.</span></span> <span data-ttu-id="421f7-133">它将路由请求，和 Web API 不知道的任何内容时，web API 创建控制器`IProductRepository`。</span><span class="sxs-lookup"><span data-stu-id="421f7-133">Web API creates the controller when it routes the request, and Web API doesn't know anything about `IProductRepository`.</span></span> <span data-ttu-id="421f7-134">这是 Web API 依赖关系解析程序的作用所在。</span><span class="sxs-lookup"><span data-stu-id="421f7-134">This is where the Web API dependency resolver comes in.</span></span>

## <a name="the-web-api-dependency-resolver"></a><span data-ttu-id="421f7-135">Web API 依赖关系解析程序</span><span class="sxs-lookup"><span data-stu-id="421f7-135">The Web API Dependency Resolver</span></span>

<span data-ttu-id="421f7-136">Web API 定义**IDependencyResolver**用于解析的依赖关系接口。</span><span class="sxs-lookup"><span data-stu-id="421f7-136">Web API defines the **IDependencyResolver** interface for resolving dependencies.</span></span> <span data-ttu-id="421f7-137">下面是该接口的定义：</span><span class="sxs-lookup"><span data-stu-id="421f7-137">Here is the definition of the interface:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample6.cs)]

<span data-ttu-id="421f7-138">**IDependencyScope**接口有两个方法：</span><span class="sxs-lookup"><span data-stu-id="421f7-138">The **IDependencyScope** interface has two methods:</span></span>

- <span data-ttu-id="421f7-139">**GetService**创建类型的一个实例。</span><span class="sxs-lookup"><span data-stu-id="421f7-139">**GetService** creates one instance of a type.</span></span>
- <span data-ttu-id="421f7-140">**Getservices 进行提取**创建指定类型的对象的集合。</span><span class="sxs-lookup"><span data-stu-id="421f7-140">**GetServices** creates a collection of objects of a specified type.</span></span>

<span data-ttu-id="421f7-141">**IDependencyResolver**方法继承**IDependencyScope** ，并将添加**如何**方法。</span><span class="sxs-lookup"><span data-stu-id="421f7-141">The **IDependencyResolver** method inherits **IDependencyScope** and adds the **BeginScope** method.</span></span> <span data-ttu-id="421f7-142">我将在本教程后面讨论的作用域。</span><span class="sxs-lookup"><span data-stu-id="421f7-142">I'll talk about scopes later in this tutorial.</span></span>

<span data-ttu-id="421f7-143">当 Web API 创建一个控制器实例时，它将首先调用**IDependencyResolver.GetService**、 传入的控制器类型。</span><span class="sxs-lookup"><span data-stu-id="421f7-143">When Web API creates a controller instance, it first calls **IDependencyResolver.GetService**, passing in the controller type.</span></span> <span data-ttu-id="421f7-144">此可扩展性挂钩可用于创建控制器，解析任何依赖项。</span><span class="sxs-lookup"><span data-stu-id="421f7-144">You can use this extensibility hook to create the controller, resolving any dependencies.</span></span> <span data-ttu-id="421f7-145">如果**GetService**返回 null，Web API 控制器类上的无参数构造函数的查找。</span><span class="sxs-lookup"><span data-stu-id="421f7-145">If **GetService** returns null, Web API looks for a parameterless constructor on the controller class.</span></span>

## <a name="dependency-resolution-with-the-unity-container"></a><span data-ttu-id="421f7-146">利用 Unity 容器解析依赖项</span><span class="sxs-lookup"><span data-stu-id="421f7-146">Dependency Resolution with the Unity Container</span></span>

<span data-ttu-id="421f7-147">尽管您可以编写的完整**IDependencyResolver**从零开始，该接口的实现真正旨在充当 Web API 和现有的 IoC 容器之间的桥。</span><span class="sxs-lookup"><span data-stu-id="421f7-147">Although you could write a complete **IDependencyResolver** implementation from scratch, the interface is really designed to act as bridge between Web API and existing IoC containers.</span></span>

<span data-ttu-id="421f7-148">IoC 容器是一个软件组件，负责管理依赖项。</span><span class="sxs-lookup"><span data-stu-id="421f7-148">An IoC container is a software component that is responsible for managing dependencies.</span></span> <span data-ttu-id="421f7-149">您向容器注册类型，然后使用容器来创建对象。</span><span class="sxs-lookup"><span data-stu-id="421f7-149">You register types with the container, and then use the container to create objects.</span></span> <span data-ttu-id="421f7-150">容器自动找出的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="421f7-150">The container automatically figures out the dependency relations.</span></span> <span data-ttu-id="421f7-151">多个 IoC 容器还可用于控制对象生存期和作用域等。</span><span class="sxs-lookup"><span data-stu-id="421f7-151">Many IoC containers also allow you to control things like object lifetime and scope.</span></span>

> [!NOTE]
> <span data-ttu-id="421f7-152">"IoC"代表"控制反转"，这是一个框架，其中调用到应用程序代码中的一般模式。</span><span class="sxs-lookup"><span data-stu-id="421f7-152">"IoC" stands for "inversion of control", which is a general pattern where a framework calls into application code.</span></span> <span data-ttu-id="421f7-153">IoC 容器构造您的对象，其中"反转"常用控制流。</span><span class="sxs-lookup"><span data-stu-id="421f7-153">An IoC container constructs your objects for you, which "inverts" the usual flow of control.</span></span>


<span data-ttu-id="421f7-154">对于本教程中，我们将使用[Unity](https://msdn.microsoft.com/library/ff647202.aspx)从 Microsoft 模式&amp;实践。</span><span class="sxs-lookup"><span data-stu-id="421f7-154">For this tutorial, we'll use [Unity](https://msdn.microsoft.com/library/ff647202.aspx) from Microsoft Patterns &amp; Practices.</span></span> <span data-ttu-id="421f7-155">(其他常用库包括[Castle Windsor](http://www.castleproject.org/)， [Spring.Net](http://www.springframework.net/)， [Autofac](https://code.google.com/p/autofac/)， [Ninject](http://www.ninject.org/)，和[StructureMap](http://structuremap.github.io/documentation/).)可以使用 NuGet 包管理器安装 Unity。</span><span class="sxs-lookup"><span data-stu-id="421f7-155">(Other popular libraries include [Castle Windsor](http://www.castleproject.org/), [Spring.Net](http://www.springframework.net/), [Autofac](https://code.google.com/p/autofac/), [Ninject](http://www.ninject.org/), and [StructureMap](http://structuremap.github.io/documentation/).) You can use NuGet Package Manager to install Unity.</span></span> <span data-ttu-id="421f7-156">从**工具**在 Visual Studio 中，选择菜单**NuGet 包管理器**，然后选择**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="421f7-156">From the **Tools** menu in Visual Studio, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="421f7-157">在包管理器控制台窗口中，键入以下命令：</span><span class="sxs-lookup"><span data-stu-id="421f7-157">In the Package Manager Console window, type the following command:</span></span>

[!code-console[Main](dependency-injection/samples/sample7.cmd)]

<span data-ttu-id="421f7-158">下面是实现**IDependencyResolver**包装 Unity 容器。</span><span class="sxs-lookup"><span data-stu-id="421f7-158">Here is an implementation of **IDependencyResolver** that wraps a Unity container.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample8.cs)]

> [!NOTE]
> <span data-ttu-id="421f7-159">如果**GetService**方法无法解析类型，它应返回**null**。</span><span class="sxs-lookup"><span data-stu-id="421f7-159">If the **GetService** method cannot resolve a type, it should return **null**.</span></span> <span data-ttu-id="421f7-160">如果**getservices 进行提取**方法不能将类型解析，则它应返回一个空集合对象。</span><span class="sxs-lookup"><span data-stu-id="421f7-160">If the **GetServices** method cannot resolve a type, it should return an empty collection object.</span></span> <span data-ttu-id="421f7-161">不会引发异常的未知类型。</span><span class="sxs-lookup"><span data-stu-id="421f7-161">Don't throw exceptions for unknown types.</span></span>


## <a name="configuring-the-dependency-resolver"></a><span data-ttu-id="421f7-162">配置依赖关系解析程序</span><span class="sxs-lookup"><span data-stu-id="421f7-162">Configuring the Dependency Resolver</span></span>

<span data-ttu-id="421f7-163">设置依赖关系解析程序上**DependencyResolver**属性的全局**HttpConfiguration**对象。</span><span class="sxs-lookup"><span data-stu-id="421f7-163">Set the dependency resolver on the **DependencyResolver** property of the global **HttpConfiguration** object.</span></span>

<span data-ttu-id="421f7-164">下面的代码寄存器`IProductRepository`使用 Unity 的接口，然后创建`UnityResolver`。</span><span class="sxs-lookup"><span data-stu-id="421f7-164">The following code registers the `IProductRepository` interface with Unity and then creates a `UnityResolver`.</span></span>

[!code-csharp[Main](dependency-injection/samples/sample9.cs)]

## <a name="dependency-scope-and-controller-lifetime"></a><span data-ttu-id="421f7-165">依赖关系范围和控制器生命周期</span><span class="sxs-lookup"><span data-stu-id="421f7-165">Dependency Scope and Controller Lifetime</span></span>

<span data-ttu-id="421f7-166">创建控制器的每个请求。</span><span class="sxs-lookup"><span data-stu-id="421f7-166">Controllers are created per request.</span></span> <span data-ttu-id="421f7-167">若要管理对象生存期**IDependencyResolver**使用的概念*作用域*。</span><span class="sxs-lookup"><span data-stu-id="421f7-167">To manage object lifetimes, **IDependencyResolver** uses the concept of a *scope*.</span></span>

<span data-ttu-id="421f7-168">依赖关系解析程序附加到**HttpConfiguration**对象具有全局作用域。</span><span class="sxs-lookup"><span data-stu-id="421f7-168">The dependency resolver attached to the **HttpConfiguration** object has global scope.</span></span> <span data-ttu-id="421f7-169">当 Web API 创建一个控制器时，它将调用**如何**。</span><span class="sxs-lookup"><span data-stu-id="421f7-169">When Web API creates a controller, it calls **BeginScope**.</span></span> <span data-ttu-id="421f7-170">此方法返回**IDependencyScope**表示子范围。</span><span class="sxs-lookup"><span data-stu-id="421f7-170">This method returns an **IDependencyScope** that represents a child scope.</span></span>

<span data-ttu-id="421f7-171">然后调用 web API **GetService**子作用域，以创建控制器上。</span><span class="sxs-lookup"><span data-stu-id="421f7-171">Web API then calls **GetService** on the child scope to create the controller.</span></span> <span data-ttu-id="421f7-172">请求完成后，调用 Web API **Dispose**在子作用域上。</span><span class="sxs-lookup"><span data-stu-id="421f7-172">When request is complete, Web API calls **Dispose** on the child scope.</span></span> <span data-ttu-id="421f7-173">使用**Dispose**方法来释放的控制器的依赖项。</span><span class="sxs-lookup"><span data-stu-id="421f7-173">Use the **Dispose** method to dispose of the controller's dependencies.</span></span>

<span data-ttu-id="421f7-174">如何实现**如何**取决于在 IoC 容器。</span><span class="sxs-lookup"><span data-stu-id="421f7-174">How you implement **BeginScope** depends on the IoC container.</span></span> <span data-ttu-id="421f7-175">对于 Unity 中，作用域对应于在子容器：</span><span class="sxs-lookup"><span data-stu-id="421f7-175">For Unity, scope corresponds to a child container:</span></span>

[!code-csharp[Main](dependency-injection/samples/sample10.cs)]

<span data-ttu-id="421f7-176">大多数 IoC 容器具有类似的等效项。</span><span class="sxs-lookup"><span data-stu-id="421f7-176">Most IoC containers have similar equivalents.</span></span>
