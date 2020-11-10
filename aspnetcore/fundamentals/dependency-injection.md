---
title: ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 31db9aea9e0b7ed21cae2f87fbb9e2e649782697
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234460"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="fb6c8-103">ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="fb6c8-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fb6c8-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="fb6c8-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fb6c8-106">有关 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fb6c8-107">若要了解如何在 Web 应用以外的应用程序中使用依赖关系注入，请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="fb6c8-108">有关选项的依赖项注入的详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="fb6c8-109">本主题介绍 ASP.NET Core 中的依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="fb6c8-110">有关使用依赖关系注入的主要文档包含在 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="fb6c8-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fb6c8-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fb6c8-112">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="fb6c8-112">Overview of dependency injection</span></span>

<span data-ttu-id="fb6c8-113">依赖项是指另一个对象所依赖的对象。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="fb6c8-114">使用其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="fb6c8-115">类可以创建 `MyDependency` 类的实例，以便利用其 `WriteMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="fb6c8-116">在以下示例中，`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="fb6c8-117">该类创建并直接依赖于 `MyDependency` 类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="fb6c8-118">代码依赖项（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fb6c8-119">要用不同的实现替换 `MyDependency`，必须修改 `IndexModel` 类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="fb6c8-120">如果 `MyDependency` 具有依赖项，则必须由 `IndexModel` 类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="fb6c8-121">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码将分散在整个应用中。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fb6c8-122">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fb6c8-123">应用需使用模拟或存根 `MyDependency` 类，而该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fb6c8-124">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fb6c8-125">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fb6c8-126">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fb6c8-127">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fb6c8-128">服务通常已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fb6c8-129">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fb6c8-130">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fb6c8-131">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="fb6c8-132">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="fb6c8-133">示例应用使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fb6c8-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 方法使用范围内生存期（单个请求的生存期）注册服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="fb6c8-135">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="fb6c8-136">在示例应用中，请求 `IMyDependency` 服务并用于调用 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="fb6c8-137">通过使用 DI 模式，表示控制器：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="fb6c8-138">不使用具体类型 `MyDependency`，仅使用它实现的 `IMyDependency` 接口。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="fb6c8-139">这样可以轻松地更改控制器使用的实现，而无需修改控制器。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="fb6c8-140">不创建 `MyDependency` 的实例，这由 DI 容器创建。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="fb6c8-141">可以通过使用内置日志记录 API 来改善 `IMyDependency` 接口的实现：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="fb6c8-142">更新的 `ConfigureServices` 方法注册新的 `IMyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="fb6c8-143">`MyDependency2` 依赖于 <xref:Microsoft.Extensions.Logging.ILogger%601>，并在构造函数中对其进行请求。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="fb6c8-144">`ILogger<TCategoryName>` 是[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="fb6c8-145">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fb6c8-146">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fb6c8-147">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fb6c8-148">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="fb6c8-149">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="fb6c8-150">在依赖项注入术语中，服务：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="fb6c8-151">通常是向其他对象提供服务的对象，如 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="fb6c8-152">与 Web 服务无关，尽管服务可能使用 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="fb6c8-153">框架提供可靠的[日志记录](xref:fundamentals/logging/index)系统。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="fb6c8-154">编写上述示例中的 `IMyDependency` 实现来演示基本的 DI，而不是来实现日志记录。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="fb6c8-155">大多数应用都不需要编写记录器。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="fb6c8-156">下面的代码演示如何使用默认日志记录，这不要求在 `ConfigureServices` 中注册任何服务：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="fb6c8-157">使用前面的代码时，无需更新 `ConfigureServices`，因为框架提供[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="fb6c8-158">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-158">Services injected into Startup</span></span>

<span data-ttu-id="fb6c8-159">服务可以注入 `Startup` 构造函数和 `Startup.Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="fb6c8-160">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="fb6c8-161">任何向 DI 容器注册的服务都可以注入 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="fb6c8-162">有关详细信息，请参阅 <xref:fundamentals/startup> 和[访问 Startup 中的配置](xref:fundamentals/configuration/index#access-configuration-in-startup)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="fb6c8-163">使用扩展方法注册服务组</span><span class="sxs-lookup"><span data-stu-id="fb6c8-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="fb6c8-164">ASP.NET Core 框架使用一种约定来注册一组相关服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="fb6c8-165">约定使用单个 `Add{GROUP_NAME}` 扩展方法来注册该框架功能所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="fb6c8-166">例如，<Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> 扩展方法会注册 MVC 控制器所需的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="fb6c8-167">下面的代码通过个人用户帐户由 :::no-loc(Razor)::: 页面模板生成，并演示如何使用扩展方法 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 和 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> 将其他服务添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="fb6c8-168">服务生存期</span><span class="sxs-lookup"><span data-stu-id="fb6c8-168">Service lifetimes</span></span>

<span data-ttu-id="fb6c8-169">请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)中的[服务生存期](/dotnet/core/extensions/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="fb6c8-170">要在中间件中使用范围内服务，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="fb6c8-171">将服务注入中间件的 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fb6c8-172">使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)会引发运行时异常，因为它强制使范围内服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="fb6c8-173">[生存期和注册选项](#lifetime-and-registration-options)部分中的示例演示了 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="fb6c8-174">使用[基于工厂的中间件](xref:fundamentals/middleware/extensibility)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="fb6c8-175">使用此方法注册的中间件按客户端请求（连接）激活，这也使范围内服务可注入中间件的 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="fb6c8-176">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="fb6c8-177">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="fb6c8-177">Service registration methods</span></span>

<span data-ttu-id="fb6c8-178">请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)中的[服务注册方法](/dotnet/core/extensions/dependency-injection#service-registration-methods)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="fb6c8-179">在[为测试模拟类型](xref:test/integration-tests#inject-mock-services)时，使用多个实现很常见。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="fb6c8-180">仅使用实现类型注册服务等效于使用相同的实现和服务类型注册该服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="fb6c8-181">因此，我们不能使用捕获显式服务类型的方法来注册服务的多个实现。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="fb6c8-182">这些方法可以注册服务的多个实例，但它们都具有相同的实现类型 。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="fb6c8-183">上述任何服务注册方法都可用于注册同一服务类型的多个服务实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="fb6c8-184">下面的示例以 `IMyDependency` 作为服务类型调用 `AddSingleton` 两次。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="fb6c8-185">第二次对 `AddSingleton` 的调用在解析为 `IMyDependency` 时替代上一次调用，在通过 `IEnumerable<IMyDependency>` 解析多个服务时添加到上一次调用。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="fb6c8-186">通过 `IEnumerable<{SERVICE}>` 解析服务时，服务按其注册顺序显示。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="fb6c8-187">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="fb6c8-187">Constructor injection behavior</span></span>

<span data-ttu-id="fb6c8-188">请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)中的[构造函数注入行为](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fb6c8-189">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="fb6c8-189">Entity Framework contexts</span></span>

<span data-ttu-id="fb6c8-190">默认情况下，使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="fb6c8-191">要使用其他生存期，请使用 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 重载来指定生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="fb6c8-192">给定生存期的服务不应使用生存期比服务生存期短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fb6c8-193">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="fb6c8-193">Lifetime and registration options</span></span>

<span data-ttu-id="fb6c8-194">为了演示服务生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="fb6c8-195">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="fb6c8-196">以下 `Operation` 类实现了前面的所有接口。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="fb6c8-197">`Operation` 构造函数生成 GUID，并将最后 4 个字符存储在 `OperationId` 属性中：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="fb6c8-198">`Startup.ConfigureServices` 方法根据命名生存期创建 `Operation` 类的多个注册：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="fb6c8-199">示例应用一并演示了请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="fb6c8-200">`IndexModel` 和中间件请求每种 `IOperation` 类型，并记录各自的 `OperationId`：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="fb6c8-201">与 `IndexModel` 类似，中间件会解析相同的服务：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="fb6c8-202">范围内服务必须在 `InvokeAsync` 方法中进行解析：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="fb6c8-203">记录器输出显示：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-203">The logger output shows:</span></span>

* <span data-ttu-id="fb6c8-204">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-204">*Transient* objects are always different.</span></span> <span data-ttu-id="fb6c8-205">`IndexModel` 和中间件中的临时 `OperationId` 值不同。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="fb6c8-206">范围内对象对每个请求而言是相同的，但在请求之间不同。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="fb6c8-207">单一实例对象对于每个请求是相同的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="fb6c8-208">若要减少日志记录输出，请在 appsettings.Development.json 文件中设置“Logging:LogLevel:Microsoft:Error”：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="fb6c8-209">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-209">Call services from main</span></span>

<span data-ttu-id="fb6c8-210">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的作用域服务。 </span><span class="sxs-lookup"><span data-stu-id="fb6c8-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fb6c8-211">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="fb6c8-212">以下示例演示如何访问范围内 `IMyDependency` 服务并在 `Program.Main` 中调用其 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="fb6c8-213">作用域验证</span><span class="sxs-lookup"><span data-stu-id="fb6c8-213">Scope validation</span></span>

<span data-ttu-id="fb6c8-214">请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)中的[构造函数注入行为](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="fb6c8-215">有关详细信息，请参阅[作用域验证](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="fb6c8-216">请求服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-216">Request Services</span></span>

<span data-ttu-id="fb6c8-217">ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="fb6c8-218">从请求内部请求服务时，将从 `RequestServices` 集合解析服务及其依赖项。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="fb6c8-219">为每个请求创建一个作用域，`RequestServices` 公开作用域服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="fb6c8-220">只要请求处于活动状态，所有作用域服务都有效。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="fb6c8-221">与解析 `RequestServices` 集合中的服务相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="fb6c8-222">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fb6c8-223">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-223">Design services for dependency injection</span></span>

<span data-ttu-id="fb6c8-224">在设计能够进行依赖注入的服务时：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="fb6c8-225">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="fb6c8-226">通过将应用设计为改用单一实例服务，避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="fb6c8-227">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fb6c8-228">直接实例化会将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fb6c8-229">不在服务中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fb6c8-230">如果一个类有过多注入依赖项，这可能表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fb6c8-231">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="fb6c8-232">请记住，:::no-loc(Razor)::: Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-232">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fb6c8-233">服务释放</span><span class="sxs-lookup"><span data-stu-id="fb6c8-233">Disposal of services</span></span>

<span data-ttu-id="fb6c8-234">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="fb6c8-235">从容器中解析的服务绝对不应由开发人员释放。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="fb6c8-236">如果类型或工厂注册为单一实例，则容器自动释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="fb6c8-237">在下面的示例中，服务由服务容器创建，并自动释放：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="fb6c8-238">每次刷新索引页后，调试控制台显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="fb6c8-239">不由服务容器创建的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-239">Services not created by the service container</span></span>

<span data-ttu-id="fb6c8-240">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="fb6c8-241">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-241">In the preceding code:</span></span>

* <span data-ttu-id="fb6c8-242">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="fb6c8-243">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="fb6c8-244">开发人员负责释放服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="fb6c8-245">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="fb6c8-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="fb6c8-246">请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)中的[暂时和共享实例的 IDisposable 指南](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="fb6c8-247">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="fb6c8-247">Default service container replacement</span></span>

<span data-ttu-id="fb6c8-248">请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)中的[默认服务容器替换](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="fb6c8-249">建议</span><span class="sxs-lookup"><span data-stu-id="fb6c8-249">Recommendations</span></span>

<span data-ttu-id="fb6c8-250">请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)中的[建议](/dotnet/core/extensions/dependency-injection-guidelines#recommendations)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="fb6c8-251">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="fb6c8-252">例如，可以使用 DI 代替时，不要调用 <xref:System.IServiceProvider.GetService%2A>  来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="fb6c8-253">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-253">**Incorrect:**</span></span>

    ![错误代码](dependency-injection/_static/bad.png)

  <span data-ttu-id="fb6c8-255">**正确** ：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-255">**Correct** :</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="fb6c8-256">要避免的另一个服务定位器变体是注入需在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="fb6c8-257">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="fb6c8-258">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="fb6c8-259">避免在 `ConfigureServices` 中调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="fb6c8-260">当开发人员想要在 `ConfigureServices` 中解析服务时，通常会调用 `BuildServiceProvider`。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="fb6c8-261">例如，假设 `LoginPath` 从配置中加载。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="fb6c8-262">避免采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-262">Avoid the following approach:</span></span>

  ![调用 BuildServiceProvider 的错误代码](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="fb6c8-264">在上图中，选择 `services.BuildServiceProvider` 下的绿色波浪线将显示以下 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="fb6c8-265">ASP0000 从应用程序代码调用“BuildServiceProvider”会导致创建单一实例服务的其他副本。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="fb6c8-266">考虑依赖项注入服务等替代项作为“Configure”的参数。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="fb6c8-267">调用 `BuildServiceProvider` 会创建第二个容器，该容器可创建残缺的单一实例并导致跨多个容器引用对象图。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="fb6c8-268">获取 `LoginPath` 的正确方法是使用选项模式对 DI 的内置支持：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="fb6c8-269">可释放的暂时性服务由容器捕获以进行释放。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="fb6c8-270">如果从顶级容器解析，这会变为内存泄漏。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="fb6c8-271">启用范围验证，确保应用没有捕获范围内服务的单一实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="fb6c8-272">有关详细信息，请参阅[作用域验证](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="fb6c8-273">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fb6c8-274">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fb6c8-275">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fb6c8-276">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="fb6c8-277">DI 中适用于多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="fb6c8-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="fb6c8-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 是用于在 ASP.NET Core 上构建模块化多租户应用程序的应用程序框架。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="fb6c8-279">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="fb6c8-280">请参阅 [Orchard Core 示例](https://github.com/OrchardCMS/OrchardCore.Samples)，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fb6c8-281">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-281">Framework-provided services</span></span>

<span data-ttu-id="fb6c8-282">`Startup.ConfigureServices` 方法注册应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fb6c8-283">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="fb6c8-284">对于基于 ASP.NET Core 模板的应用，该框架会注册 250 个以上的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="fb6c8-285">下表列出了框架注册的这些服务的一小部分：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="fb6c8-286">服务类型</span><span class="sxs-lookup"><span data-stu-id="fb6c8-286">Service Type</span></span>                                                                                    | <span data-ttu-id="fb6c8-287">生存期</span><span class="sxs-lookup"><span data-stu-id="fb6c8-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fb6c8-288">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="fb6c8-289">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="fb6c8-290">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="fb6c8-291">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="fb6c8-292">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="fb6c8-293">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="fb6c8-294">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="fb6c8-295">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="fb6c8-296">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="fb6c8-297">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="fb6c8-298">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="fb6c8-299">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="fb6c8-300">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="fb6c8-301">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="fb6c8-302">其他资源</span><span class="sxs-lookup"><span data-stu-id="fb6c8-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="fb6c8-303">用于 DI 应用开发的 NDC 会议模式</span><span class="sxs-lookup"><span data-stu-id="fb6c8-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fb6c8-304">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="fb6c8-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="fb6c8-305">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="fb6c8-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="fb6c8-306">显式依赖关系原则</span><span class="sxs-lookup"><span data-stu-id="fb6c8-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="fb6c8-307">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="fb6c8-308">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-308">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb6c8-309">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="fb6c8-310">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fb6c8-311">有关 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fb6c8-312">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fb6c8-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fb6c8-313">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="fb6c8-313">Overview of dependency injection</span></span>

<span data-ttu-id="fb6c8-314">依赖项是指另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="fb6c8-315">查看以下具有 `WriteMessage` 方法的 `MyDependency` 类，此类为应用中其他类所依赖：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="fb6c8-316">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于其他类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="fb6c8-317">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="fb6c8-318">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="fb6c8-319">代码依赖关系（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fb6c8-320">若要用另一个实现替换 `MyDependency`，则必须修改类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="fb6c8-321">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="fb6c8-322">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码将分散在整个应用中。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fb6c8-323">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fb6c8-324">应用需使用模拟或存根 `MyDependency` 类，而该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fb6c8-325">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fb6c8-326">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fb6c8-327">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fb6c8-328">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="fb6c8-329">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fb6c8-330">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fb6c8-331">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fb6c8-332">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="fb6c8-333">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="fb6c8-334">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="fb6c8-335">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fb6c8-336">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fb6c8-337">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fb6c8-338">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="fb6c8-339">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="fb6c8-340">`IMyDependency` 在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fb6c8-341">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="fb6c8-342">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="fb6c8-343">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fb6c8-344">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="fb6c8-345">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="fb6c8-346">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-346">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="fb6c8-347">例如，`services.AddMvc()` 添加 :::no-loc(Razor)::: Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-347">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="fb6c8-348">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="fb6c8-349">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册组。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-349">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="fb6c8-350">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-350">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="fb6c8-351">可通过类的构造函数请求服务实例，在该函数中可使用服务并将其分配给私有字段。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-351">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="fb6c8-352">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-352">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="fb6c8-353">在示例应用中，请求 `IMyDependency` 实例并将其用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-353">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="fb6c8-354">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-354">Services injected into Startup</span></span>

<span data-ttu-id="fb6c8-355">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-355">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="fb6c8-356">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-356">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="fb6c8-357">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-357">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="fb6c8-358">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-358">Framework-provided services</span></span>

<span data-ttu-id="fb6c8-359">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-359">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fb6c8-360">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-360">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="fb6c8-361">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-361">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="fb6c8-362">下表列出了框架注册的服务的一小部分。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-362">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="fb6c8-363">服务类型</span><span class="sxs-lookup"><span data-stu-id="fb6c8-363">Service Type</span></span> | <span data-ttu-id="fb6c8-364">生存期</span><span class="sxs-lookup"><span data-stu-id="fb6c8-364">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="fb6c8-365">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-365">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="fb6c8-366">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-366">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="fb6c8-367">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-367">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="fb6c8-368">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-368">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="fb6c8-369">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-369">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="fb6c8-370">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="fb6c8-371">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-371">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="fb6c8-372">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-372">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="fb6c8-373">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-373">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="fb6c8-374">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-374">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="fb6c8-375">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-375">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="fb6c8-376">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-376">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="fb6c8-377">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-377">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="fb6c8-378">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-378">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="fb6c8-379">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-379">Register additional services with extension methods</span></span>

<span data-ttu-id="fb6c8-380">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-380">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="fb6c8-381">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-381">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="fb6c8-382">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-382">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="fb6c8-383">服务生存期</span><span class="sxs-lookup"><span data-stu-id="fb6c8-383">Service lifetimes</span></span>

<span data-ttu-id="fb6c8-384">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-384">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="fb6c8-385">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-385">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="fb6c8-386">暂时</span><span class="sxs-lookup"><span data-stu-id="fb6c8-386">Transient</span></span>

<span data-ttu-id="fb6c8-387">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-387">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="fb6c8-388">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-388">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="fb6c8-389">在处理请求的应用中，在请求结束时会释放暂时服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-389">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="fb6c8-390">作用域</span><span class="sxs-lookup"><span data-stu-id="fb6c8-390">Scoped</span></span>

<span data-ttu-id="fb6c8-391">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 针对每个客户端请求（连接）创建一次。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-391">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="fb6c8-392">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-392">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="fb6c8-393">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-393">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fb6c8-394">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-394">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="fb6c8-395">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-395">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="fb6c8-396">单例</span><span class="sxs-lookup"><span data-stu-id="fb6c8-396">Singleton</span></span>

<span data-ttu-id="fb6c8-397">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-397">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="fb6c8-398">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-398">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="fb6c8-399">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-399">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="fb6c8-400">不要在类中实现单一实例设计模式并提供用户代码来管理对象的生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-400">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="fb6c8-401">在处理请求的应用中，当应用关闭并释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-401">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="fb6c8-402">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-402">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="fb6c8-403">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-403">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="fb6c8-404">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="fb6c8-404">Service registration methods</span></span>

<span data-ttu-id="fb6c8-405">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-405">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="fb6c8-406">方法</span><span class="sxs-lookup"><span data-stu-id="fb6c8-406">Method</span></span> | <span data-ttu-id="fb6c8-407">自动</span><span class="sxs-lookup"><span data-stu-id="fb6c8-407">Automatic</span></span><br><span data-ttu-id="fb6c8-408">对象</span><span class="sxs-lookup"><span data-stu-id="fb6c8-408">object</span></span><br><span data-ttu-id="fb6c8-409">释放</span><span class="sxs-lookup"><span data-stu-id="fb6c8-409">disposal</span></span> | <span data-ttu-id="fb6c8-410">多种</span><span class="sxs-lookup"><span data-stu-id="fb6c8-410">Multiple</span></span><br><span data-ttu-id="fb6c8-411">实现</span><span class="sxs-lookup"><span data-stu-id="fb6c8-411">implementations</span></span> | <span data-ttu-id="fb6c8-412">传递参数</span><span class="sxs-lookup"><span data-stu-id="fb6c8-412">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="fb6c8-413">示例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-413">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="fb6c8-414">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-414">Yes</span></span> | <span data-ttu-id="fb6c8-415">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-415">Yes</span></span> | <span data-ttu-id="fb6c8-416">否</span><span class="sxs-lookup"><span data-stu-id="fb6c8-416">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="fb6c8-417">示例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-417">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="fb6c8-418">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-418">Yes</span></span> | <span data-ttu-id="fb6c8-419">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-419">Yes</span></span> | <span data-ttu-id="fb6c8-420">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-420">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="fb6c8-421">示例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-421">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="fb6c8-422">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-422">Yes</span></span> | <span data-ttu-id="fb6c8-423">否</span><span class="sxs-lookup"><span data-stu-id="fb6c8-423">No</span></span> | <span data-ttu-id="fb6c8-424">否</span><span class="sxs-lookup"><span data-stu-id="fb6c8-424">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="fb6c8-425">示例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-425">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="fb6c8-426">否</span><span class="sxs-lookup"><span data-stu-id="fb6c8-426">No</span></span> | <span data-ttu-id="fb6c8-427">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-427">Yes</span></span> | <span data-ttu-id="fb6c8-428">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-428">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="fb6c8-429">示例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-429">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="fb6c8-430">否</span><span class="sxs-lookup"><span data-stu-id="fb6c8-430">No</span></span> | <span data-ttu-id="fb6c8-431">否</span><span class="sxs-lookup"><span data-stu-id="fb6c8-431">No</span></span> | <span data-ttu-id="fb6c8-432">是</span><span class="sxs-lookup"><span data-stu-id="fb6c8-432">Yes</span></span> |

<span data-ttu-id="fb6c8-433">要详细了解释放类型，请参阅[服务释放](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-433">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="fb6c8-434">多个实现的常见场景是[为了测试而模拟各个类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-434">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="fb6c8-435">仅使用实现类型注册服务等效于使用相同的实现和服务类型注册该服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-435">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="fb6c8-436">因此，我们不能使用捕获显式服务类型的方法来注册服务的多个实现。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-436">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="fb6c8-437">这些方法可以注册服务的多个实例，但它们都具有相同的实现类型 。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-437">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="fb6c8-438">上述任何服务注册方法都可用于注册同一服务类型的多个服务实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-438">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="fb6c8-439">下面的示例以 `IMyDependency` 作为服务类型调用 `AddSingleton` 两次。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-439">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="fb6c8-440">第二次对 `AddSingleton` 的调用在解析为 `IMyDependency` 时替代上一次调用，在通过 `IEnumerable<IMyDependency>` 解析多个服务时添加到上一次调用。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-440">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="fb6c8-441">通过 `IEnumerable<{SERVICE}>` 解析服务时，服务按其注册顺序显示。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-441">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="fb6c8-442">框架还提供 `TryAdd{LIFETIME}` 扩展方法，只有当尚未注册某个实现时，才注册该服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-442">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="fb6c8-443">在下面的示例中，对 `AddSingleton` 的调用会将 `MyDependency` 注册为 `IMyDependency`的实现。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-443">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="fb6c8-444">对 `TryAddSingleton` 的调用没有任何作用，因为 `IMyDependency` 已有一个已注册的实现。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-444">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="fb6c8-445">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-445">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="fb6c8-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="fb6c8-447">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-447">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="fb6c8-448">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-448">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="fb6c8-449">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-449">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="fb6c8-450">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-450">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="fb6c8-451">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-451">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="fb6c8-452">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-452">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="fb6c8-453">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="fb6c8-453">Constructor injection behavior</span></span>

<span data-ttu-id="fb6c8-454">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-454">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="fb6c8-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="fb6c8-456">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-456">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="fb6c8-457">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-457">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="fb6c8-458">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-458">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="fb6c8-459">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-459">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="fb6c8-460">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-460">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fb6c8-461">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="fb6c8-461">Entity Framework contexts</span></span>

<span data-ttu-id="fb6c8-462">通常将实体框架上下文添加到使用[作用域生存期](#service-lifetimes)的服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-462">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="fb6c8-463">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则默认生存期为作用域。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-463">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="fb6c8-464">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-464">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fb6c8-465">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="fb6c8-465">Lifetime and registration options</span></span>

<span data-ttu-id="fb6c8-466">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-466">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="fb6c8-467">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-467">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="fb6c8-468">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-468">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="fb6c8-469">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-469">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="fb6c8-470">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-470">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="fb6c8-471">当通过依赖关系注入请求 `OperationService` 时，它将基于依赖服务的生存期接收每个服务的新实例或现有实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-471">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="fb6c8-472">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-472">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="fb6c8-473">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-473">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="fb6c8-474">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-474">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="fb6c8-475">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-475">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="fb6c8-476">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-476">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="fb6c8-477">如果创建了一次单例服务和单例实例服务并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-477">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="fb6c8-478">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-478">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="fb6c8-479">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-479">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="fb6c8-480">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-480">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="fb6c8-481">示例应用演示了各个请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-481">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="fb6c8-482">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-482">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="fb6c8-483">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-483">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="fb6c8-484">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-484">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="fb6c8-485">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-485">**First request:**</span></span>

<span data-ttu-id="fb6c8-486">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-486">Controller operations:</span></span>

<span data-ttu-id="fb6c8-487">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="fb6c8-487">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="fb6c8-488">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fb6c8-488">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fb6c8-489">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fb6c8-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fb6c8-490">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fb6c8-490">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fb6c8-491">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-491">`OperationService` operations:</span></span>

<span data-ttu-id="fb6c8-492">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="fb6c8-492">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="fb6c8-493">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fb6c8-493">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fb6c8-494">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fb6c8-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fb6c8-495">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fb6c8-495">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fb6c8-496">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-496">**Second request:**</span></span>

<span data-ttu-id="fb6c8-497">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-497">Controller operations:</span></span>

<span data-ttu-id="fb6c8-498">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="fb6c8-498">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="fb6c8-499">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fb6c8-499">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fb6c8-500">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fb6c8-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fb6c8-501">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fb6c8-501">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fb6c8-502">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-502">`OperationService` operations:</span></span>

<span data-ttu-id="fb6c8-503">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="fb6c8-503">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="fb6c8-504">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fb6c8-504">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fb6c8-505">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fb6c8-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fb6c8-506">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fb6c8-506">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fb6c8-507">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-507">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="fb6c8-508">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-508">*Transient* objects are always different.</span></span> <span data-ttu-id="fb6c8-509">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-509">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="fb6c8-510">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-510">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="fb6c8-511">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-511">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="fb6c8-512">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-512">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="fb6c8-513">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-513">Call services from main</span></span>

<span data-ttu-id="fb6c8-514">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的作用域服务。 </span><span class="sxs-lookup"><span data-stu-id="fb6c8-514">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fb6c8-515">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-515">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="fb6c8-516">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-516">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="fb6c8-517">作用域验证</span><span class="sxs-lookup"><span data-stu-id="fb6c8-517">Scope validation</span></span>

<span data-ttu-id="fb6c8-518">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-518">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="fb6c8-519">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-519">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="fb6c8-520">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-520">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="fb6c8-521">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-521">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="fb6c8-522">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-522">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="fb6c8-523">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-523">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="fb6c8-524">如果作用域服务创建于根容器，则该服务的生存期会实际上提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-524">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="fb6c8-525">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-525">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="fb6c8-526">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-526">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="fb6c8-527">请求服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-527">Request Services</span></span>

<span data-ttu-id="fb6c8-528">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-528">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="fb6c8-529">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-529">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="fb6c8-530">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-530">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="fb6c8-531">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-531">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="fb6c8-532">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-532">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="fb6c8-533">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-533">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="fb6c8-534">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-534">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fb6c8-535">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-535">Design services for dependency injection</span></span>

<span data-ttu-id="fb6c8-536">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-536">Best practices are to:</span></span>

* <span data-ttu-id="fb6c8-537">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-537">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="fb6c8-538">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-538">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="fb6c8-539">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-539">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="fb6c8-540">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-540">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fb6c8-541">直接实例化会将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-541">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fb6c8-542">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-542">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fb6c8-543">如果一个类似乎有过多的依赖关系注入，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-543">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fb6c8-544">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-544">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="fb6c8-545">请记住，:::no-loc(Razor)::: Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-545">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="fb6c8-546">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-546">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fb6c8-547">服务释放</span><span class="sxs-lookup"><span data-stu-id="fb6c8-547">Disposal of services</span></span>

<span data-ttu-id="fb6c8-548">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-548">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="fb6c8-549">如果实例是通过用户代码添加到容器中，则不会自动释放该实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-549">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="fb6c8-550">在下面的示例中，服务由服务容器创建，并自动释放：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-550">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="fb6c8-551">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-551">In the following example:</span></span>

* <span data-ttu-id="fb6c8-552">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-552">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="fb6c8-553">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-553">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="fb6c8-554">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-554">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="fb6c8-555">服务如果未通过开发者代码显式释放，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-555">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="fb6c8-556">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="fb6c8-556">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="fb6c8-557">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="fb6c8-557">Transient, limited lifetime</span></span>

<span data-ttu-id="fb6c8-558">**方案**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-558">**Scenario**</span></span>

<span data-ttu-id="fb6c8-559">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-559">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="fb6c8-560">在根作用域内解析实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-560">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="fb6c8-561">应在作用域结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-561">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="fb6c8-562">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-562">**Solution**</span></span>

<span data-ttu-id="fb6c8-563">使用工厂模式在父作用域外创建实例。 </span><span class="sxs-lookup"><span data-stu-id="fb6c8-563">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="fb6c8-564">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-564">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="fb6c8-565">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-565">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="fb6c8-566">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-566">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="fb6c8-567">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-567">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="fb6c8-568">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="fb6c8-568">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="fb6c8-569">**方案**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-569">**Scenario**</span></span>

<span data-ttu-id="fb6c8-570">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-570">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="fb6c8-571">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-571">**Solution**</span></span>

<span data-ttu-id="fb6c8-572">为实例注册作用域生存期。 </span><span class="sxs-lookup"><span data-stu-id="fb6c8-572">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="fb6c8-573">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-573">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="fb6c8-574">使用作用域的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-574">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="fb6c8-575">在生存期应结束时释放作用域。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-575">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="fb6c8-576">通用准则</span><span class="sxs-lookup"><span data-stu-id="fb6c8-576">General Guidelines</span></span>

* <span data-ttu-id="fb6c8-577">不要为<xref:System.IDisposable> 实例注册暂时作用域。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-577">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="fb6c8-578">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-578">Use the factory pattern instead.</span></span>
* <span data-ttu-id="fb6c8-579">不要在根作用域内解析暂时或作用域 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-579">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="fb6c8-580">唯一的一般性例外是应用创建/重建和释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-580">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="fb6c8-581">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-581">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="fb6c8-582"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-582">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="fb6c8-583">作用域应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-583">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="fb6c8-584"> 作用域不区分层次，并且在各作用域之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-584">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="fb6c8-585">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="fb6c8-585">Default service container replacement</span></span>

<span data-ttu-id="fb6c8-586">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-586">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="fb6c8-587">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-587">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="fb6c8-588">属性注入</span><span class="sxs-lookup"><span data-stu-id="fb6c8-588">Property injection</span></span>
* <span data-ttu-id="fb6c8-589">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="fb6c8-589">Injection based on name</span></span>
* <span data-ttu-id="fb6c8-590">子容器</span><span class="sxs-lookup"><span data-stu-id="fb6c8-590">Child containers</span></span>
* <span data-ttu-id="fb6c8-591">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="fb6c8-591">Custom lifetime management</span></span>
* <span data-ttu-id="fb6c8-592">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="fb6c8-592">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="fb6c8-593">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="fb6c8-593">Convention-based registration</span></span>

<span data-ttu-id="fb6c8-594">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-594">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="fb6c8-595">Autofac</span><span class="sxs-lookup"><span data-stu-id="fb6c8-595">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="fb6c8-596">DryIoc</span><span class="sxs-lookup"><span data-stu-id="fb6c8-596">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="fb6c8-597">Grace</span><span class="sxs-lookup"><span data-stu-id="fb6c8-597">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="fb6c8-598">LightInject</span><span class="sxs-lookup"><span data-stu-id="fb6c8-598">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="fb6c8-599">Lamar</span><span class="sxs-lookup"><span data-stu-id="fb6c8-599">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="fb6c8-600">Stashbox</span><span class="sxs-lookup"><span data-stu-id="fb6c8-600">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="fb6c8-601">Unity</span><span class="sxs-lookup"><span data-stu-id="fb6c8-601">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="fb6c8-602">线程安全</span><span class="sxs-lookup"><span data-stu-id="fb6c8-602">Thread safety</span></span>

<span data-ttu-id="fb6c8-603">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-603">Create thread-safe singleton services.</span></span> <span data-ttu-id="fb6c8-604">如果单例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-604">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="fb6c8-605">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-605">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="fb6c8-606">像类型 (`static`) 构造函数一样，它保证单个线程只调用一次。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-606">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="fb6c8-607">建议</span><span class="sxs-lookup"><span data-stu-id="fb6c8-607">Recommendations</span></span>

* <span data-ttu-id="fb6c8-608">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-608">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="fb6c8-609">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-609">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="fb6c8-610">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-610">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="fb6c8-611">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-611">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="fb6c8-612">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-612">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="fb6c8-613">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-613">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="fb6c8-614">最好通过 DI 请求实际项。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-614">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="fb6c8-615">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-615">Avoid static access to services.</span></span> <span data-ttu-id="fb6c8-616">例如，避免静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-616">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="fb6c8-617">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-617">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="fb6c8-618">可以使用 DI 代替时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-618">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="fb6c8-619">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="fb6c8-619">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="fb6c8-620">**正确** ：</span><span class="sxs-lookup"><span data-stu-id="fb6c8-620">**Correct** :</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="fb6c8-621">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-621">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="fb6c8-622">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-622">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="fb6c8-623">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-623">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fb6c8-624">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-624">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fb6c8-625">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-625">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fb6c8-626">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="fb6c8-626">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb6c8-627">其他资源</span><span class="sxs-lookup"><span data-stu-id="fb6c8-627">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fb6c8-628">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="fb6c8-628">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="fb6c8-629">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="fb6c8-629">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="fb6c8-630">显式依赖关系原则</span><span class="sxs-lookup"><span data-stu-id="fb6c8-630">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="fb6c8-631">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="fb6c8-631">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="fb6c8-632">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="fb6c8-632">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
