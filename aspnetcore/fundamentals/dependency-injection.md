---
title: 在 ASP.NET Core 依赖注入
author: guardrex
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 5e1522e0819d989a7029c2928c1c33624c1774c7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899354"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="f8d8c-103">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="f8d8c-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="f8d8c-104">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f8d8c-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f8d8c-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f8d8c-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f8d8c-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f8d8c-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f8d8c-108">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="f8d8c-108">Overview of dependency injection</span></span>

<span data-ttu-id="f8d8c-109">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f8d8c-110">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f8d8c-111">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f8d8c-112">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f8d8c-113">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-113">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f8d8c-114">`MyDependency` 类是 `HomeController` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-114">The `MyDependency` class is a dependency of the `HomeController` class:</span></span>

```csharp
public class HomeController : Controller
{
    MyDependency _dependency = new MyDependency();

    public async Task<IActionResult> Index()
    {
        await _dependency.WriteMessage(
            "HomeController.Index created this message.");

        return View();
    }
}
```

::: moniker-end

<span data-ttu-id="f8d8c-115">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-115">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f8d8c-116">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-116">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f8d8c-117">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-117">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f8d8c-118">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-118">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f8d8c-119">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-119">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f8d8c-120">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-120">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f8d8c-121">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-121">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f8d8c-122">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-122">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f8d8c-123">使用接口抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-123">The use of an interface to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f8d8c-124">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-124">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f8d8c-125">ASP.NET Core 提供了一个内置的服务容器 [IServiceProvider](/dotnet/api/system.iserviceprovider)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-125">ASP.NET Core provides a built-in service container, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span></span> <span data-ttu-id="f8d8c-126">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-126">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f8d8c-127">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-127">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f8d8c-128">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-128">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f8d8c-129">在[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-129">In the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8d8c-130">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-130">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8d8c-131">`MyDependency` 在其构造函数中请求 [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-131">`MyDependency` requests an [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) in its constructor.</span></span> <span data-ttu-id="f8d8c-132">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-132">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f8d8c-133">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-133">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f8d8c-134">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-134">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f8d8c-135">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-135">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f8d8c-136">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-136">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f8d8c-137">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-137">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8d8c-138">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-138">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f8d8c-139">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-139">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f8d8c-140">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-140">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f8d8c-141">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-141">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f8d8c-142">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-142">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f8d8c-143">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-143">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f8d8c-144">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-144">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f8d8c-145">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-145">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f8d8c-146">如果服务的构造函数需要基元（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入基元：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-146">If the service's constructor requires a primitive, such as a `string`, the primitive can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f8d8c-147">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-147">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f8d8c-148">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-148">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f8d8c-149">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-149">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/MyDependencyController.cs?name=snippet1&highlight=3,5-8,13-14)]

::: moniker-end

## <a name="framework-provided-services"></a><span data-ttu-id="f8d8c-150">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="f8d8c-150">Framework-provided services</span></span>

<span data-ttu-id="f8d8c-151">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-151">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f8d8c-152">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 定义了以下服务（具体取决于[配置主机的方式](xref:fundamentals/index#host)）：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-152">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="f8d8c-153">服务类型</span><span class="sxs-lookup"><span data-stu-id="f8d8c-153">Service Type</span></span> | <span data-ttu-id="f8d8c-154">生存期</span><span class="sxs-lookup"><span data-stu-id="f8d8c-154">Lifetime</span></span> |
| ------------ | -------- |
| [<span data-ttu-id="f8d8c-155">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span><span class="sxs-lookup"><span data-stu-id="f8d8c-155">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | <span data-ttu-id="f8d8c-156">暂时</span><span class="sxs-lookup"><span data-stu-id="f8d8c-156">Transient</span></span> |
| [<span data-ttu-id="f8d8c-157">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="f8d8c-157">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | <span data-ttu-id="f8d8c-158">单例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-158">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-159">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="f8d8c-159">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | <span data-ttu-id="f8d8c-160">单例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-160">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-161">Microsoft.AspNetCore.Hosting.IStartup</span><span class="sxs-lookup"><span data-stu-id="f8d8c-161">Microsoft.AspNetCore.Hosting.IStartup</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | <span data-ttu-id="f8d8c-162">单例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-162">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-163">Microsoft.AspNetCore.Hosting.IStartupFilter</span><span class="sxs-lookup"><span data-stu-id="f8d8c-163">Microsoft.AspNetCore.Hosting.IStartupFilter</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | <span data-ttu-id="f8d8c-164">暂时</span><span class="sxs-lookup"><span data-stu-id="f8d8c-164">Transient</span></span> |
| [<span data-ttu-id="f8d8c-165">Microsoft.AspNetCore.Hosting.Server.IServer</span><span class="sxs-lookup"><span data-stu-id="f8d8c-165">Microsoft.AspNetCore.Hosting.Server.IServer</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | <span data-ttu-id="f8d8c-166">单例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-166">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-167">Microsoft.AspNetCore.Http.IHttpContextFactory</span><span class="sxs-lookup"><span data-stu-id="f8d8c-167">Microsoft.AspNetCore.Http.IHttpContextFactory</span></span>](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | <span data-ttu-id="f8d8c-168">暂时</span><span class="sxs-lookup"><span data-stu-id="f8d8c-168">Transient</span></span> |
| [<span data-ttu-id="f8d8c-169">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="f8d8c-169">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.logging.ilogger) | <span data-ttu-id="f8d8c-170">单例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-170">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-171">Microsoft.Extensions.Logging.ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="f8d8c-171">Microsoft.Extensions.Logging.ILoggerFactory</span></span>](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | <span data-ttu-id="f8d8c-172">单例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-172">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-173">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span><span class="sxs-lookup"><span data-stu-id="f8d8c-173">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span></span>](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | <span data-ttu-id="f8d8c-174">单一实例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-174">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-175">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="f8d8c-175">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | <span data-ttu-id="f8d8c-176">暂时</span><span class="sxs-lookup"><span data-stu-id="f8d8c-176">Transient</span></span> |
| [<span data-ttu-id="f8d8c-177">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="f8d8c-177">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.ioptions-1) | <span data-ttu-id="f8d8c-178">单一实例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-178">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-179">System.Diagnostics.DiagnosticSource</span><span class="sxs-lookup"><span data-stu-id="f8d8c-179">System.Diagnostics.DiagnosticSource</span></span>](/dotnet/core/api/system.diagnostics.diagnosticsource) | <span data-ttu-id="f8d8c-180">单一实例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-180">Singleton</span></span> |
| [<span data-ttu-id="f8d8c-181">System.Diagnostics.DiagnosticListener</span><span class="sxs-lookup"><span data-stu-id="f8d8c-181">System.Diagnostics.DiagnosticListener</span></span>](/dotnet/core/api/system.diagnostics.diagnosticlistener) | <span data-ttu-id="f8d8c-182">单例</span><span class="sxs-lookup"><span data-stu-id="f8d8c-182">Singleton</span></span> |

<span data-ttu-id="f8d8c-183">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-183">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f8d8c-184">以下代码是如何使用扩展方法 [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)、[AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity) 和 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc) 向容器添加其他服务的示例：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-184">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), and [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

<span data-ttu-id="f8d8c-185">有关详细信息，请参阅 API 文档中的 [ServiceCollection 类](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-185">For more information, see the [ServiceCollection Class](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f8d8c-186">服务生存期</span><span class="sxs-lookup"><span data-stu-id="f8d8c-186">Service lifetimes</span></span>

<span data-ttu-id="f8d8c-187">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-187">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f8d8c-188">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-188">ASP.NET Core services can be configured with the following lifetimes:</span></span>

<span data-ttu-id="f8d8c-189">**暂时**</span><span class="sxs-lookup"><span data-stu-id="f8d8c-189">**Transient**</span></span>

<span data-ttu-id="f8d8c-190">暂时生存期服务是每次请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-190">Transient lifetime services are created each time they're requested.</span></span> <span data-ttu-id="f8d8c-191">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-191">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="f8d8c-192">**作用域（Scoped）**</span><span class="sxs-lookup"><span data-stu-id="f8d8c-192">**Scoped**</span></span>

<span data-ttu-id="f8d8c-193">作用域生存期服务以每个请求一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-193">Scoped lifetime services are created once per request.</span></span>

> [!WARNING]
> <span data-ttu-id="f8d8c-194">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-194">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f8d8c-195">请不要通过构造函数注入进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-195">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f8d8c-196">有关更多信息，请参见<xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-196">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="f8d8c-197">**单例**</span><span class="sxs-lookup"><span data-stu-id="f8d8c-197">**Singleton**</span></span>

<span data-ttu-id="f8d8c-198">单一实例生存期服务是在第一次请求时（或者在运行 `ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-198">Singleton lifetime services are created the first time they're requested (or when `ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f8d8c-199">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-199">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f8d8c-200">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-200">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f8d8c-201">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-201">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="f8d8c-202">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-202">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f8d8c-203">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-203">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f8d8c-204">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="f8d8c-204">Constructor injection behavior</span></span>

<span data-ttu-id="f8d8c-205">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-205">Services can be resolved by two mechanisms:</span></span>

* `IServiceProvider`
* <span data-ttu-id="f8d8c-206">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; 允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-206">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f8d8c-207">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-207">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f8d8c-208">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-208">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f8d8c-209">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，构造函数注入需要 *public* 构造函数。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-209">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="f8d8c-210">当服务由 `ActivatorUtilities` 解析时，构造函数注入要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-210">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f8d8c-211">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-211">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f8d8c-212">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="f8d8c-212">Entity Framework contexts</span></span>

<span data-ttu-id="f8d8c-213">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为请求。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-213">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the request.</span></span> <span data-ttu-id="f8d8c-214">如果在注册数据库上下文时，<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-214">The default lifetime is scoped if a lifetime isn't specified by an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> overload when registering the database context.</span></span> <span data-ttu-id="f8d8c-215">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-215">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f8d8c-216">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="f8d8c-216">Lifetime and registration options</span></span>

<span data-ttu-id="f8d8c-217">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-217">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f8d8c-218">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-218">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8d8c-219">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-219">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f8d8c-220">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-220">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8d8c-221">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-221">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f8d8c-222">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-222">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f8d8c-223">如果在请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-223">If transient services are created when requested, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f8d8c-224">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-224">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f8d8c-225">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-225">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f8d8c-226">如果按请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-226">If scoped services are created per request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a request.</span></span> <span data-ttu-id="f8d8c-227">在请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-227">Across requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f8d8c-228">如果单一数据库和单一实例服务只创建一次并在所有请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-228">If singleton and singleton-instance services are created once and used across all requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8d8c-229">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-229">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=12-15,18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="f8d8c-230">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-230">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f8d8c-231">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-231">It's clear when this type is in use (its GUID is all zeroes).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f8d8c-232">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-232">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f8d8c-233">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-233">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f8d8c-234">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-234">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f8d8c-235">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-235">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f8d8c-236">示例应用包含一个 `OperationsController`，它会请求各种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-236">The sample app includes an `OperationsController` that requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f8d8c-237">`Index` 操作将服务设置为 `ViewBag` 以显示服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-237">The `Index` action sets the services into the `ViewBag` for display of the service's `OperationId` values:</span></span>

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/OperationsController.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8d8c-238">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-238">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f8d8c-239">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="f8d8c-239">**First request:**</span></span>

<span data-ttu-id="f8d8c-240">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-240">Controller operations:</span></span>

<span data-ttu-id="f8d8c-241">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f8d8c-241">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f8d8c-242">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8d8c-242">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8d8c-243">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8d8c-243">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8d8c-244">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8d8c-244">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8d8c-245">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-245">`OperationService` operations:</span></span>

<span data-ttu-id="f8d8c-246">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f8d8c-246">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f8d8c-247">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8d8c-247">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8d8c-248">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8d8c-248">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8d8c-249">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8d8c-249">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8d8c-250">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="f8d8c-250">**Second request:**</span></span>

<span data-ttu-id="f8d8c-251">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-251">Controller operations:</span></span>

<span data-ttu-id="f8d8c-252">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f8d8c-252">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f8d8c-253">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8d8c-253">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8d8c-254">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8d8c-254">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8d8c-255">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8d8c-255">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8d8c-256">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-256">`OperationService` operations:</span></span>

<span data-ttu-id="f8d8c-257">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f8d8c-257">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f8d8c-258">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8d8c-258">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8d8c-259">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8d8c-259">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8d8c-260">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8d8c-260">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8d8c-261">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-261">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f8d8c-262">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-262">*Transient* objects are always different.</span></span> <span data-ttu-id="f8d8c-263">请注意，第一个和第二个请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-263">Note that the transient `OperationId` value for both the first and second requests are different for both `OperationService` operations and across requests.</span></span> <span data-ttu-id="f8d8c-264">为每个服务和请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-264">A new instance is provided to each service and request.</span></span>
* <span data-ttu-id="f8d8c-265">有作用域的对象在一个请求内是相同的，但在请求之间是不同的。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-265">*Scoped* objects are the same within a request but different across requests.</span></span>
* <span data-ttu-id="f8d8c-266">单一实例对象对每个对象和每个请求都是相同的（不管 `ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-266">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f8d8c-267">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="f8d8c-267">Call services from main</span></span>

<span data-ttu-id="f8d8c-268">采用 [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) 创建 [IServiceScope ](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope)，以解析应用作用域中有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-268">Create an [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) with [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f8d8c-269">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-269">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f8d8c-270">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-270">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
public static void Main(string[] args)
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

    host.Run();
}
```

## <a name="scope-validation"></a><span data-ttu-id="f8d8c-271">作用域验证</span><span class="sxs-lookup"><span data-stu-id="f8d8c-271">Scope validation</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f8d8c-272">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-272">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f8d8c-273">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-273">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f8d8c-274">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-274">Scoped services aren't directly or indirectly injected into singletons.</span></span>

::: moniker-end

<span data-ttu-id="f8d8c-275">调用 [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) 时，会创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-275">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="f8d8c-276">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-276">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f8d8c-277">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-277">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f8d8c-278">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-278">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f8d8c-279">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-279">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f8d8c-280">有关更多信息，请参见<xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-280">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="f8d8c-281">请求服务</span><span class="sxs-lookup"><span data-stu-id="f8d8c-281">Request Services</span></span>

<span data-ttu-id="f8d8c-282">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-282">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) collection.</span></span>

<span data-ttu-id="f8d8c-283">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-283">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f8d8c-284">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-284">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f8d8c-285">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-285">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f8d8c-286">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-286">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="f8d8c-287">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-287">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="f8d8c-288">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-288">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f8d8c-289">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="f8d8c-289">Design services for dependency injection</span></span>

<span data-ttu-id="f8d8c-290">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-290">Best practices are to:</span></span>

* <span data-ttu-id="f8d8c-291">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-291">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f8d8c-292">避免进行有状态的静态方法调用。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-292">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="f8d8c-293">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-293">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f8d8c-294">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-294">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f8d8c-295">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-295">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f8d8c-296">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-296">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f8d8c-297">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-297">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f8d8c-298">请记住，Razor Pages 页模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-298">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f8d8c-299">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-299">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f8d8c-300">服务处理</span><span class="sxs-lookup"><span data-stu-id="f8d8c-300">Disposal of services</span></span>

<span data-ttu-id="f8d8c-301">容器为其创建的 `IDisposable` 类型调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-301">The container calls `Dispose` for the `IDisposable` types it creates.</span></span> <span data-ttu-id="f8d8c-302">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-302">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

::: moniker range="= aspnetcore-1.0"

> [!NOTE]
> <span data-ttu-id="f8d8c-303">在 ASP.NET Core 1.0 中，容器将对所有 `IDisposable` 对象调用 dispose，包括那些并非由它创建的对象。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-303">In ASP.NET Core 1.0, the container calls dispose on *all* `IDisposable` objects, including those it didn't create.</span></span>

::: moniker-end

## <a name="default-service-container-replacement"></a><span data-ttu-id="f8d8c-304">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="f8d8c-304">Default service container replacement</span></span>

<span data-ttu-id="f8d8c-305">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-305">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f8d8c-306">我们建议使用内置容器，除非你需要的特定功能不受它支持。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-306">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="f8d8c-307">内置容器中找不到第三方容器支持的某些功能：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-307">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="f8d8c-308">属性注入</span><span class="sxs-lookup"><span data-stu-id="f8d8c-308">Property injection</span></span>
* <span data-ttu-id="f8d8c-309">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="f8d8c-309">Injection based on name</span></span>
* <span data-ttu-id="f8d8c-310">子容器</span><span class="sxs-lookup"><span data-stu-id="f8d8c-310">Child containers</span></span>
* <span data-ttu-id="f8d8c-311">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="f8d8c-311">Custom lifetime management</span></span>
* <span data-ttu-id="f8d8c-312">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="f8d8c-312">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="f8d8c-313">有关支持适配器的部分容器列表，请参阅[依赖关系注入 readme.md 文件](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-313">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="f8d8c-314">以下示例将内置容器替换为 [Autofac](https://autofac.org/)：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-314">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="f8d8c-315">安装适当的容器包：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-315">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="f8d8c-316">Autofac</span><span class="sxs-lookup"><span data-stu-id="f8d8c-316">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="f8d8c-317">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="f8d8c-317">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="f8d8c-318">在 `Startup.ConfigureServices` 中配置容器并返回 `IServiceProvider`：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-318">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    <span data-ttu-id="f8d8c-319">要使用第三方容器，`Startup.ConfigureServices` 必须返回 `IServiceProvider`。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-319">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="f8d8c-320">在 `DefaultModule` 中配置 Autofac：</span><span class="sxs-lookup"><span data-stu-id="f8d8c-320">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="f8d8c-321">在运行时，使用 Autofac 来解析类型，并注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-321">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="f8d8c-322">要了解有关结合使用 Autofac 和 ASP.NET Core 的详细信息，请参阅 [Autofac 文档](https://docs.autofac.org/en/latest/integration/aspnetcore.html)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-322">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="f8d8c-323">线程安全</span><span class="sxs-lookup"><span data-stu-id="f8d8c-323">Thread safety</span></span>

<span data-ttu-id="f8d8c-324">单例服务需要是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-324">Singleton services need to be thread safe.</span></span> <span data-ttu-id="f8d8c-325">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要是线程安全的，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-325">If a singleton service has a dependency on a transient service, the transient service may also need to be thread safe depending how it's used by the singleton.</span></span>

<span data-ttu-id="f8d8c-326">单个服务的工厂方法，例如 [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__) 的第二个参数，不需要是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-326">The factory method of single service, such as the second argument to [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f8d8c-327">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-327">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f8d8c-328">建议</span><span class="sxs-lookup"><span data-stu-id="f8d8c-328">Recommendations</span></span>

* <span data-ttu-id="f8d8c-329">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-329">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f8d8c-330">C# 不支持异步构造函数，因此推荐的模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-330">C# does not support asynchronous constructors, therefore the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f8d8c-331">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-331">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f8d8c-332">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-332">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f8d8c-333">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-333">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f8d8c-334">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-334">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f8d8c-335">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-335">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="f8d8c-336">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-336">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) for use elsewhere).</span></span>

* <span data-ttu-id="f8d8c-337">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-337">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="f8d8c-338">例如，可以改为使用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-338">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead.</span></span> <span data-ttu-id="f8d8c-339">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-339">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="f8d8c-340">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-340">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="f8d8c-341">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)）。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-341">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span></span>

<span data-ttu-id="f8d8c-342">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-342">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f8d8c-343">例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-343">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f8d8c-344">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-344">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f8d8c-345">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="f8d8c-345">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8d8c-346">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8d8c-346">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f8d8c-347">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="f8d8c-347">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="f8d8c-348">容器托管的应用程序设计，序言：容器属于哪里？</span><span class="sxs-lookup"><span data-stu-id="f8d8c-348">Container-Managed Application Design, Prelude: Where does the Container Belong?</span></span>](https://blogs.msdn.microsoft.com/nblumhardt/2008/12/26/container-managed-application-design-prelude-where-does-the-container-belong/)
* <span data-ttu-id="f8d8c-349">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="f8d8c-349">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="f8d8c-350">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="f8d8c-350">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f8d8c-351">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="f8d8c-351">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
