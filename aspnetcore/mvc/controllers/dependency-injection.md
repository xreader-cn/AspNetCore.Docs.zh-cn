---
title: 在 ASP.NET Core 中将依赖项注入到控制器
author: ardalis
description: 了解 ASP.NET Core MVC 控制器如何使用 ASP.NET Core 中的依赖项注入通过构造函数显式请求其依赖项。
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 9d9d0a68927da62fad8df72c868eaf4b8ada440d
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410266"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="96d4b-103">在 ASP.NET Core 中将依赖项注入到控制器</span><span class="sxs-lookup"><span data-stu-id="96d4b-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="96d4b-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="96d4b-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="96d4b-105">ASP.NET Core MVC 控制器应该通过构造函数显式请求其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="96d4b-105">ASP.NET Core MVC controllers should request their dependencies explicitly via their constructors.</span></span> <span data-ttu-id="96d4b-106">在某些情况下，单独的控制器操作可能需要服务，但在控制器级别上请求可能没有意义。</span><span class="sxs-lookup"><span data-stu-id="96d4b-106">In some instances, individual controller actions may require a service, and it may not make sense to request at the controller level.</span></span> <span data-ttu-id="96d4b-107">在此情况下，也可在操作方法上选择将服务作为参数注入。</span><span class="sxs-lookup"><span data-stu-id="96d4b-107">In this case, you can also choose to inject a service as a parameter on the action method.</span></span>

<span data-ttu-id="96d4b-108">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="96d4b-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="96d4b-109">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="96d4b-109">Dependency Injection</span></span>

<span data-ttu-id="96d4b-110">ASP.NET Core 具有对[依赖关系注入](../../fundamentals/dependency-injection.md)的内置支持，能更易于测试和维护应用程序。</span><span class="sxs-lookup"><span data-stu-id="96d4b-110">ASP.NET Core has built-in support for [dependency injection](../../fundamentals/dependency-injection.md), which makes applications easier to test and maintain.</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="96d4b-111">构造函数注入</span><span class="sxs-lookup"><span data-stu-id="96d4b-111">Constructor Injection</span></span>

<span data-ttu-id="96d4b-112">ASP.NET Core 对基于构造函数的依赖关系注入的内置支持扩展到 MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="96d4b-112">ASP.NET Core's built-in support for constructor-based dependency injection extends to MVC controllers.</span></span> <span data-ttu-id="96d4b-113">通过将服务类型作为构造函数参数添加到控制器中，ASP.NET Core 将尝试使用其内置的服务容器来解析该类型。</span><span class="sxs-lookup"><span data-stu-id="96d4b-113">By simply adding a service type to your controller as a constructor parameter, ASP.NET Core will attempt to resolve that type using its built in service container.</span></span> <span data-ttu-id="96d4b-114">通常（但不总是）使用接口来定义服务。</span><span class="sxs-lookup"><span data-stu-id="96d4b-114">Services are typically, but not always, defined using interfaces.</span></span> <span data-ttu-id="96d4b-115">例如，如果应用程序具有依赖于当前时间的业务逻辑，则可以注入一个检索时间（而不是对其进行硬编码）的服务，这将允许测试进入使用设置时间的实现。</span><span class="sxs-lookup"><span data-stu-id="96d4b-115">For example, if your application has business logic that depends on the current time, you can inject a service that retrieves the time (rather than hard-coding it), which would allow your tests to pass in implementations that use a set time.</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


<span data-ttu-id="96d4b-116">实现这样一个接口，以便在运行时使用系统时钟，此操作很简单：</span><span class="sxs-lookup"><span data-stu-id="96d4b-116">Implementing an interface like this one so that it uses the system clock at runtime is trivial:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


<span data-ttu-id="96d4b-117">准备就绪后，我们可在控制器中使用服务。</span><span class="sxs-lookup"><span data-stu-id="96d4b-117">With this in place, we can use the service in our controller.</span></span> <span data-ttu-id="96d4b-118">在此情况下，我们在 `HomeController` `Index` 方法中添加了一些逻辑，根据每天的时间向用户显示问候语。</span><span class="sxs-lookup"><span data-stu-id="96d4b-118">In this case, we have added some logic to the `HomeController` `Index` method to display a greeting to the user based on the time of day.</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

<span data-ttu-id="96d4b-119">如果现在运行应用程序，很可能会遇到错误：</span><span class="sxs-lookup"><span data-stu-id="96d4b-119">If we run the application now, we will most likely encounter an error:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

<span data-ttu-id="96d4b-120">当我们尚未在 `Startup` 类的 `ConfigureServices` 方法中配置服务时，会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="96d4b-120">This error occurs when we have not configured a service in the `ConfigureServices` method in our `Startup` class.</span></span> <span data-ttu-id="96d4b-121">若要指定应使用 `SystemDateTime` 的实例解析 `IDateTime` 的请求，请将下面列表中突出显示的行添加到 `ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="96d4b-121">To specify that requests for `IDateTime` should be resolved using an instance of `SystemDateTime`, add the highlighted line in the listing below to your `ConfigureServices` method:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> <span data-ttu-id="96d4b-122">可以使用几种不同的生存期选项（`Transient``Scoped` 或 `Singleton`）中的任意一个来实现此特定服务。</span><span class="sxs-lookup"><span data-stu-id="96d4b-122">This particular service could be implemented using any of several different lifetime options (`Transient`, `Scoped`, or `Singleton`).</span></span> <span data-ttu-id="96d4b-123">请参阅[依赖关系注入](../../fundamentals/dependency-injection.md)，了解每个作用域选项将如何影响服务的行为。</span><span class="sxs-lookup"><span data-stu-id="96d4b-123">See [Dependency Injection](../../fundamentals/dependency-injection.md) to understand how each of these scope options will affect the behavior of your service.</span></span>

<span data-ttu-id="96d4b-124">一旦服务配置完毕，运行应用程序并导航到主页应按预期显示基于时间的消息：</span><span class="sxs-lookup"><span data-stu-id="96d4b-124">Once the service has been configured, running the application and navigating to the home page should display the time-based message as expected:</span></span>

![服务器问候语](dependency-injection/_static/server-greeting.png)

>[!TIP]
> <span data-ttu-id="96d4b-126">请参阅[测试控制器逻辑](testing.md)，了解如何显式请求控制器中的依赖项以更轻松地测试代码。</span><span class="sxs-lookup"><span data-stu-id="96d4b-126">See [Test controller logic](testing.md) to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

<span data-ttu-id="96d4b-127">ASP.NET Core 的内置依赖关系注入支持请求服务的类只拥有一个构造函数。</span><span class="sxs-lookup"><span data-stu-id="96d4b-127">ASP.NET Core's built-in dependency injection supports having only a single constructor for classes requesting services.</span></span> <span data-ttu-id="96d4b-128">如果有多个构造函数，可能会收到如下异常消息：</span><span class="sxs-lookup"><span data-stu-id="96d4b-128">If you have more than one constructor, you may get an exception stating:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

<span data-ttu-id="96d4b-129">如错误消息所述，使用一个构造函数可更正此问题。</span><span class="sxs-lookup"><span data-stu-id="96d4b-129">As the error message states, you can correct this problem with the use of a single constructor.</span></span> <span data-ttu-id="96d4b-130">还可[将默认依赖关系注入容器替换为第三方实现](xref:fundamentals/dependency-injection#default-service-container-replacement)，其中许多可支持多个构造函数。</span><span class="sxs-lookup"><span data-stu-id="96d4b-130">You can also [replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement), many of which support multiple constructors.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="96d4b-131">FromServices 的操作注入</span><span class="sxs-lookup"><span data-stu-id="96d4b-131">Action Injection with FromServices</span></span>

<span data-ttu-id="96d4b-132">有时，控制器中不需要多个操作的服务。</span><span class="sxs-lookup"><span data-stu-id="96d4b-132">Sometimes you don't need a service for more than one action within your controller.</span></span> <span data-ttu-id="96d4b-133">在此情况下，将服务作为参数注入操作方法可能是有意义的。</span><span class="sxs-lookup"><span data-stu-id="96d4b-133">In this case, it may make sense to inject the service as a parameter to the action method.</span></span> <span data-ttu-id="96d4b-134">通过标记具有属性 `[FromServices]` 的参数来完成此操作，如下所示：</span><span class="sxs-lookup"><span data-stu-id="96d4b-134">This is done by marking the parameter with the attribute `[FromServices]` as shown here:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a><span data-ttu-id="96d4b-135">从控制器访问设置</span><span class="sxs-lookup"><span data-stu-id="96d4b-135">Accessing Settings from a Controller</span></span>

<span data-ttu-id="96d4b-136">从控制器中访问应用程序或配置设置是一种常见模式。</span><span class="sxs-lookup"><span data-stu-id="96d4b-136">Accessing application or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="96d4b-137">此访问应使用[配置](xref:fundamentals/configuration/index)中所述的选项模式。</span><span class="sxs-lookup"><span data-stu-id="96d4b-137">This access should use the Options pattern described in [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="96d4b-138">通常不应使用依赖关系注入直接从控制器请求设置。</span><span class="sxs-lookup"><span data-stu-id="96d4b-138">You generally shouldn't request settings directly from your controller using dependency injection.</span></span> <span data-ttu-id="96d4b-139">最好请求 `IOptions<T>` 实例，其中 `T` 是所需的配置类。</span><span class="sxs-lookup"><span data-stu-id="96d4b-139">A better approach is to request an `IOptions<T>` instance, where `T` is the configuration class you need.</span></span>

<span data-ttu-id="96d4b-140">若要使用选项模式，需要创建一个表示选项的类，例如：</span><span class="sxs-lookup"><span data-stu-id="96d4b-140">To work with the options pattern, you need to create a class that represents the options, such as this one:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

<span data-ttu-id="96d4b-141">然后，需要将应用程序配置为使用选项模型，并将配置类添加到 `ConfigureServices` 中的服务集合：</span><span class="sxs-lookup"><span data-stu-id="96d4b-141">Then you need to configure the application to use the options model and add your configuration class to the services collection in `ConfigureServices`:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> <span data-ttu-id="96d4b-142">在上面的列表中，我们要将应用程序配置为从 JSON 格式的文件中读取设置。</span><span class="sxs-lookup"><span data-stu-id="96d4b-142">In the above listing, we are configuring the application to read the settings from a JSON-formatted file.</span></span> <span data-ttu-id="96d4b-143">也可以完全使用代码配置设置，如上面注释的代码所示。</span><span class="sxs-lookup"><span data-stu-id="96d4b-143">You can also configure the settings entirely in code, as is shown in the commented code above.</span></span> <span data-ttu-id="96d4b-144">有关进一步的配置选项，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="96d4b-144">See [Configuration](xref:fundamentals/configuration/index) for further configuration options.</span></span>

<span data-ttu-id="96d4b-145">一旦指定了强类型的配置对象（在本例中为 `SampleWebSettings`）并将其添加到服务集合中，就可通过请求 `IOptions<T>` 的实例，从任何 Controller 或 Action 方法请求它（在本例中为 `IOptions<SampleWebSettings>`）。</span><span class="sxs-lookup"><span data-stu-id="96d4b-145">Once you've specified a strongly-typed configuration object (in this case, `SampleWebSettings`) and added it to the services collection, you can request it from any Controller or Action method by requesting an instance of `IOptions<T>` (in this case, `IOptions<SampleWebSettings>`).</span></span> <span data-ttu-id="96d4b-146">以下代码显示了如何从控制器请求设置：</span><span class="sxs-lookup"><span data-stu-id="96d4b-146">The following code shows how one would request the settings from a controller:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

<span data-ttu-id="96d4b-147">遵循选项模式，可将设置和配置相互分离，并确保控制器遵循 [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)（问题分离），因为它不需要知道如何或在哪里找到设置信息。</span><span class="sxs-lookup"><span data-stu-id="96d4b-147">Following the Options pattern allows settings and configuration to be decoupled from one another, and ensures the controller is following [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), since it doesn't need to know how or where to find the settings information.</span></span> <span data-ttu-id="96d4b-148">由于控制器类中没有设置类的直接实例化，因此控制器更易于进行[单元测试](testing.md)。</span><span class="sxs-lookup"><span data-stu-id="96d4b-148">It also makes the controller easier to [unit test](testing.md), since there's no direct instantiation of settings classes within the controller class.</span></span>
