---
title: 在 ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3e31be02f21f8c28c1d98d47d9a744b3a8502253
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003185"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0a8f1-103">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="0a8f1-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="0a8f1-104">作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="0a8f1-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a8f1-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0a8f1-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0a8f1-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0a8f1-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0a8f1-108">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="0a8f1-108">Overview of dependency injection</span></span>

<span data-ttu-id="0a8f1-109">依赖项  是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0a8f1-110">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0a8f1-111">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0a8f1-112">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0a8f1-113">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0a8f1-114">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0a8f1-115">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0a8f1-116">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0a8f1-117">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0a8f1-118">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0a8f1-119">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0a8f1-120">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0a8f1-121">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0a8f1-122">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0a8f1-123">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0a8f1-124">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0a8f1-125">将服务注入  到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0a8f1-126">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0a8f1-127">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0a8f1-128">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0a8f1-129">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0a8f1-130">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0a8f1-131">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0a8f1-132">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0a8f1-133">必须被解析的依赖关系的集合通常被称为“依赖关系树”  、“依赖关系图”  或“对象图”  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0a8f1-134">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0a8f1-135">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a8f1-136">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0a8f1-137">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0a8f1-138">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0a8f1-139">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0a8f1-140">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0a8f1-141">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0a8f1-142">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0a8f1-143">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0a8f1-144">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0a8f1-145">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0a8f1-146">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0a8f1-147">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0a8f1-148">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0a8f1-149">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-149">Services injected into Startup</span></span>

<span data-ttu-id="0a8f1-150">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0a8f1-151">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0a8f1-152">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0a8f1-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-153">Framework-provided services</span></span>

<span data-ttu-id="0a8f1-154">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0a8f1-155">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0a8f1-156">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0a8f1-157">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0a8f1-158">服务类型</span><span class="sxs-lookup"><span data-stu-id="0a8f1-158">Service Type</span></span> | <span data-ttu-id="0a8f1-159">生存期</span><span class="sxs-lookup"><span data-stu-id="0a8f1-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0a8f1-160">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="0a8f1-161">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="0a8f1-162">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0a8f1-163">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0a8f1-164">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0a8f1-165">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0a8f1-166">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0a8f1-167">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0a8f1-168">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0a8f1-169">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0a8f1-170">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0a8f1-171">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0a8f1-172">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0a8f1-173">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0a8f1-174">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-174">Register additional services with extension methods</span></span>

<span data-ttu-id="0a8f1-175">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0a8f1-176">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="0a8f1-177">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0a8f1-178">服务生存期</span><span class="sxs-lookup"><span data-stu-id="0a8f1-178">Service lifetimes</span></span>

<span data-ttu-id="0a8f1-179">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0a8f1-180">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0a8f1-181">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-181">Transient</span></span>

<span data-ttu-id="0a8f1-182">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0a8f1-183">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0a8f1-184">范围内</span><span class="sxs-lookup"><span data-stu-id="0a8f1-184">Scoped</span></span>

<span data-ttu-id="0a8f1-185">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0a8f1-186">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0a8f1-187">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0a8f1-188">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0a8f1-189">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-189">Singleton</span></span>

<span data-ttu-id="0a8f1-190">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0a8f1-191">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0a8f1-192">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0a8f1-193">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0a8f1-194">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0a8f1-195">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0a8f1-196">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="0a8f1-196">Service registration methods</span></span>

<span data-ttu-id="0a8f1-197">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0a8f1-198">方法</span><span class="sxs-lookup"><span data-stu-id="0a8f1-198">Method</span></span> | <span data-ttu-id="0a8f1-199">自动</span><span class="sxs-lookup"><span data-stu-id="0a8f1-199">Automatic</span></span><br><span data-ttu-id="0a8f1-200">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="0a8f1-200">object</span></span><br><span data-ttu-id="0a8f1-201">处置</span><span class="sxs-lookup"><span data-stu-id="0a8f1-201">disposal</span></span> | <span data-ttu-id="0a8f1-202">多种</span><span class="sxs-lookup"><span data-stu-id="0a8f1-202">Multiple</span></span><br><span data-ttu-id="0a8f1-203">实现</span><span class="sxs-lookup"><span data-stu-id="0a8f1-203">implementations</span></span> | <span data-ttu-id="0a8f1-204">传递参数</span><span class="sxs-lookup"><span data-stu-id="0a8f1-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0a8f1-205">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0a8f1-206">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-206">Yes</span></span> | <span data-ttu-id="0a8f1-207">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-207">Yes</span></span> | <span data-ttu-id="0a8f1-208">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0a8f1-209">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0a8f1-210">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-210">Yes</span></span> | <span data-ttu-id="0a8f1-211">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-211">Yes</span></span> | <span data-ttu-id="0a8f1-212">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0a8f1-213">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0a8f1-214">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-214">Yes</span></span> | <span data-ttu-id="0a8f1-215">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-215">No</span></span> | <span data-ttu-id="0a8f1-216">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0a8f1-217">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0a8f1-218">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-218">No</span></span> | <span data-ttu-id="0a8f1-219">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-219">Yes</span></span> | <span data-ttu-id="0a8f1-220">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0a8f1-221">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0a8f1-222">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-222">No</span></span> | <span data-ttu-id="0a8f1-223">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-223">No</span></span> | <span data-ttu-id="0a8f1-224">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-224">Yes</span></span> |

<span data-ttu-id="0a8f1-225">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0a8f1-226">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0a8f1-227">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0a8f1-228">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0a8f1-229">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0a8f1-230">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="0a8f1-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0a8f1-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。 </span><span class="sxs-lookup"><span data-stu-id="0a8f1-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0a8f1-232">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0a8f1-233">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0a8f1-234">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0a8f1-235">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0a8f1-236">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0a8f1-237">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0a8f1-238">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="0a8f1-238">Constructor injection behavior</span></span>

<span data-ttu-id="0a8f1-239">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0a8f1-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; 允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0a8f1-241">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0a8f1-242">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0a8f1-243">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0a8f1-244">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0a8f1-245">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0a8f1-246">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="0a8f1-246">Entity Framework contexts</span></span>

<span data-ttu-id="0a8f1-247">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0a8f1-248">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0a8f1-249">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0a8f1-250">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="0a8f1-250">Lifetime and registration options</span></span>

<span data-ttu-id="0a8f1-251">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0a8f1-252">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0a8f1-253">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0a8f1-254">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0a8f1-255">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0a8f1-256">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0a8f1-257">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0a8f1-258">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0a8f1-259">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0a8f1-260">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0a8f1-261">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0a8f1-262">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0a8f1-263">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0a8f1-264">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0a8f1-265">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0a8f1-266">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0a8f1-267">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0a8f1-268">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0a8f1-269">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0a8f1-270">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="0a8f1-270">**First request:**</span></span>

<span data-ttu-id="0a8f1-271">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-271">Controller operations:</span></span>

<span data-ttu-id="0a8f1-272">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0a8f1-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0a8f1-273">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a8f1-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a8f1-274">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-275">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-276">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-276">`OperationService` operations:</span></span>

<span data-ttu-id="0a8f1-277">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0a8f1-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0a8f1-278">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a8f1-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a8f1-279">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-280">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-281">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="0a8f1-281">**Second request:**</span></span>

<span data-ttu-id="0a8f1-282">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-282">Controller operations:</span></span>

<span data-ttu-id="0a8f1-283">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0a8f1-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0a8f1-284">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a8f1-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a8f1-285">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-286">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-287">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-287">`OperationService` operations:</span></span>

<span data-ttu-id="0a8f1-288">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0a8f1-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0a8f1-289">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a8f1-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a8f1-290">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-291">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-292">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0a8f1-293">暂时性  对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-293">*Transient* objects are always different.</span></span> <span data-ttu-id="0a8f1-294">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0a8f1-295">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0a8f1-296">作用域  对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0a8f1-297">单一实例  对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0a8f1-298">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-298">Call services from main</span></span>

<span data-ttu-id="0a8f1-299">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0a8f1-300">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0a8f1-301">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

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

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="0a8f1-302">作用域验证</span><span class="sxs-lookup"><span data-stu-id="0a8f1-302">Scope validation</span></span>

<span data-ttu-id="0a8f1-303">如果应用正在开发环境中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0a8f1-304">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0a8f1-305">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0a8f1-306">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0a8f1-307">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0a8f1-308">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0a8f1-309">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0a8f1-310">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0a8f1-311">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0a8f1-312">请求服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-312">Request Services</span></span>

<span data-ttu-id="0a8f1-313">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0a8f1-314">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0a8f1-315">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0a8f1-316">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0a8f1-317">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="0a8f1-318">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0a8f1-319">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0a8f1-320">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-320">Design services for dependency injection</span></span>

<span data-ttu-id="0a8f1-321">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-321">Best practices are to:</span></span>

* <span data-ttu-id="0a8f1-322">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0a8f1-323">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0a8f1-324">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0a8f1-325">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0a8f1-326">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0a8f1-327">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0a8f1-328">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0a8f1-329">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0a8f1-330">请记住，Razor Pages 页模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0a8f1-331">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0a8f1-332">服务处理</span><span class="sxs-lookup"><span data-stu-id="0a8f1-332">Disposal of services</span></span>

<span data-ttu-id="0a8f1-333">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0a8f1-334">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0a8f1-335">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0a8f1-336">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-336">In the following example:</span></span>

* <span data-ttu-id="0a8f1-337">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0a8f1-338">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0a8f1-339">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0a8f1-340">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="0a8f1-341">有关服务处理选项的讨论，请参阅 [ASP.NET Core 中处理 IDisposable 的四种方法](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0a8f1-342">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="0a8f1-342">Default service container replacement</span></span>

<span data-ttu-id="0a8f1-343">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0a8f1-344">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0a8f1-345">属性注入</span><span class="sxs-lookup"><span data-stu-id="0a8f1-345">Property injection</span></span>
* <span data-ttu-id="0a8f1-346">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="0a8f1-346">Injection based on name</span></span>
* <span data-ttu-id="0a8f1-347">子容器</span><span class="sxs-lookup"><span data-stu-id="0a8f1-347">Child containers</span></span>
* <span data-ttu-id="0a8f1-348">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="0a8f1-348">Custom lifetime management</span></span>
* <span data-ttu-id="0a8f1-349">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="0a8f1-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0a8f1-350">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="0a8f1-350">Convention-based registration</span></span>

<span data-ttu-id="0a8f1-351">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0a8f1-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="0a8f1-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0a8f1-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0a8f1-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a8f1-354">Grace</span><span class="sxs-lookup"><span data-stu-id="0a8f1-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0a8f1-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="0a8f1-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a8f1-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="0a8f1-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0a8f1-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0a8f1-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0a8f1-358">Unity</span><span class="sxs-lookup"><span data-stu-id="0a8f1-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0a8f1-359">线程安全</span><span class="sxs-lookup"><span data-stu-id="0a8f1-359">Thread safety</span></span>

<span data-ttu-id="0a8f1-360">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="0a8f1-361">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0a8f1-362">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0a8f1-363">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0a8f1-364">建议</span><span class="sxs-lookup"><span data-stu-id="0a8f1-364">Recommendations</span></span>

* <span data-ttu-id="0a8f1-365">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0a8f1-366">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0a8f1-367">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0a8f1-368">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0a8f1-369">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0a8f1-370">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0a8f1-371">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0a8f1-372">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0a8f1-373">避免使用服务定位器模式  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0a8f1-374">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0a8f1-375">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="0a8f1-375">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="0a8f1-376">**正确**：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-376">**Correct**:</span></span>

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

* <span data-ttu-id="0a8f1-377">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0a8f1-378">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="0a8f1-379">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0a8f1-380">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0a8f1-381">例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0a8f1-382">DI 是静态/全局对象访问模式的替代方法  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0a8f1-383">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="0a8f1-384">DI 中多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="0a8f1-384">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="0a8f1-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-385">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="0a8f1-386">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-386">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="0a8f1-387">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-387">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a8f1-388">其他资源</span><span class="sxs-lookup"><span data-stu-id="0a8f1-388">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0a8f1-389">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="0a8f1-389">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="0a8f1-390">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="0a8f1-390">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="0a8f1-391">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0a8f1-391">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0a8f1-392">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-392">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a8f1-393">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-393">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0a8f1-394">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-394">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0a8f1-395">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0a8f1-395">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0a8f1-396">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="0a8f1-396">Overview of dependency injection</span></span>

<span data-ttu-id="0a8f1-397">依赖项  是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-397">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0a8f1-398">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-398">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0a8f1-399">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-399">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0a8f1-400">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-400">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0a8f1-401">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-401">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0a8f1-402">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-402">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0a8f1-403">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-403">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0a8f1-404">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-404">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0a8f1-405">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-405">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0a8f1-406">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-406">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0a8f1-407">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-407">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0a8f1-408">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-408">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0a8f1-409">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-409">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0a8f1-410">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-410">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0a8f1-411">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-411">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0a8f1-412">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-412">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0a8f1-413">将服务注入  到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-413">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0a8f1-414">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-414">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0a8f1-415">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-415">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0a8f1-416">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-416">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0a8f1-417">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-417">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0a8f1-418">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-418">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0a8f1-419">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-419">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0a8f1-420">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-420">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0a8f1-421">必须被解析的依赖关系的集合通常被称为“依赖关系树”  、“依赖关系图”  或“对象图”  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-421">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0a8f1-422">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-422">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0a8f1-423">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-423">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0a8f1-424">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-424">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0a8f1-425">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-425">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0a8f1-426">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-426">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0a8f1-427">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-427">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0a8f1-428">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-428">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0a8f1-429">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-429">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0a8f1-430">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-430">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0a8f1-431">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-431">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0a8f1-432">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-432">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0a8f1-433">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-433">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0a8f1-434">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-434">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0a8f1-435">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-435">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0a8f1-436">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-436">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0a8f1-437">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-437">Services injected into Startup</span></span>

<span data-ttu-id="0a8f1-438">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-438">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0a8f1-439">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-439">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0a8f1-440">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-440">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0a8f1-441">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-441">Framework-provided services</span></span>

<span data-ttu-id="0a8f1-442">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-442">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0a8f1-443">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-443">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0a8f1-444">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-444">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0a8f1-445">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-445">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0a8f1-446">服务类型</span><span class="sxs-lookup"><span data-stu-id="0a8f1-446">Service Type</span></span> | <span data-ttu-id="0a8f1-447">生存期</span><span class="sxs-lookup"><span data-stu-id="0a8f1-447">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0a8f1-448">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0a8f1-449">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0a8f1-450">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-450">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0a8f1-451">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-451">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0a8f1-452">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-452">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0a8f1-453">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-453">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0a8f1-454">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0a8f1-455">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-455">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0a8f1-456">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-456">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0a8f1-457">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-457">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0a8f1-458">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-458">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0a8f1-459">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-459">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0a8f1-460">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-460">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0a8f1-461">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-461">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0a8f1-462">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-462">Register additional services with extension methods</span></span>

<span data-ttu-id="0a8f1-463">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-463">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0a8f1-464">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-464">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="0a8f1-465">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-465">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0a8f1-466">服务生存期</span><span class="sxs-lookup"><span data-stu-id="0a8f1-466">Service lifetimes</span></span>

<span data-ttu-id="0a8f1-467">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-467">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0a8f1-468">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-468">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0a8f1-469">暂时</span><span class="sxs-lookup"><span data-stu-id="0a8f1-469">Transient</span></span>

<span data-ttu-id="0a8f1-470">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-470">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0a8f1-471">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-471">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0a8f1-472">范围内</span><span class="sxs-lookup"><span data-stu-id="0a8f1-472">Scoped</span></span>

<span data-ttu-id="0a8f1-473">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-473">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0a8f1-474">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-474">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0a8f1-475">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-475">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0a8f1-476">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-476">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0a8f1-477">单例</span><span class="sxs-lookup"><span data-stu-id="0a8f1-477">Singleton</span></span>

<span data-ttu-id="0a8f1-478">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-478">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0a8f1-479">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-479">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0a8f1-480">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-480">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0a8f1-481">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-481">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0a8f1-482">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-482">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0a8f1-483">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-483">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0a8f1-484">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="0a8f1-484">Service registration methods</span></span>

<span data-ttu-id="0a8f1-485">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-485">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0a8f1-486">方法</span><span class="sxs-lookup"><span data-stu-id="0a8f1-486">Method</span></span> | <span data-ttu-id="0a8f1-487">自动</span><span class="sxs-lookup"><span data-stu-id="0a8f1-487">Automatic</span></span><br><span data-ttu-id="0a8f1-488">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="0a8f1-488">object</span></span><br><span data-ttu-id="0a8f1-489">处置</span><span class="sxs-lookup"><span data-stu-id="0a8f1-489">disposal</span></span> | <span data-ttu-id="0a8f1-490">多种</span><span class="sxs-lookup"><span data-stu-id="0a8f1-490">Multiple</span></span><br><span data-ttu-id="0a8f1-491">实现</span><span class="sxs-lookup"><span data-stu-id="0a8f1-491">implementations</span></span> | <span data-ttu-id="0a8f1-492">传递参数</span><span class="sxs-lookup"><span data-stu-id="0a8f1-492">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0a8f1-493">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-493">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0a8f1-494">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-494">Yes</span></span> | <span data-ttu-id="0a8f1-495">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-495">Yes</span></span> | <span data-ttu-id="0a8f1-496">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-496">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0a8f1-497">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-497">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0a8f1-498">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-498">Yes</span></span> | <span data-ttu-id="0a8f1-499">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-499">Yes</span></span> | <span data-ttu-id="0a8f1-500">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-500">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0a8f1-501">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-501">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0a8f1-502">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-502">Yes</span></span> | <span data-ttu-id="0a8f1-503">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-503">No</span></span> | <span data-ttu-id="0a8f1-504">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-504">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0a8f1-505">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-505">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0a8f1-506">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-506">No</span></span> | <span data-ttu-id="0a8f1-507">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-507">Yes</span></span> | <span data-ttu-id="0a8f1-508">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-508">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0a8f1-509">示例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-509">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0a8f1-510">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-510">No</span></span> | <span data-ttu-id="0a8f1-511">否</span><span class="sxs-lookup"><span data-stu-id="0a8f1-511">No</span></span> | <span data-ttu-id="0a8f1-512">是</span><span class="sxs-lookup"><span data-stu-id="0a8f1-512">Yes</span></span> |

<span data-ttu-id="0a8f1-513">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-513">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0a8f1-514">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-514">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0a8f1-515">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-515">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0a8f1-516">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-516">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0a8f1-517">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-517">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0a8f1-518">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="0a8f1-518">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0a8f1-519">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。 </span><span class="sxs-lookup"><span data-stu-id="0a8f1-519">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0a8f1-520">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-520">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0a8f1-521">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-521">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0a8f1-522">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-522">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0a8f1-523">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-523">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0a8f1-524">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-524">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0a8f1-525">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-525">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0a8f1-526">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="0a8f1-526">Constructor injection behavior</span></span>

<span data-ttu-id="0a8f1-527">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-527">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0a8f1-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; 允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-528"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0a8f1-529">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-529">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0a8f1-530">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-530">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0a8f1-531">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-531">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0a8f1-532">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-532">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0a8f1-533">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-533">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0a8f1-534">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="0a8f1-534">Entity Framework contexts</span></span>

<span data-ttu-id="0a8f1-535">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-535">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0a8f1-536">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-536">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0a8f1-537">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-537">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0a8f1-538">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="0a8f1-538">Lifetime and registration options</span></span>

<span data-ttu-id="0a8f1-539">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-539">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0a8f1-540">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-540">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0a8f1-541">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-541">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0a8f1-542">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-542">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0a8f1-543">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-543">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0a8f1-544">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-544">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0a8f1-545">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-545">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0a8f1-546">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-546">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0a8f1-547">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-547">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0a8f1-548">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-548">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0a8f1-549">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-549">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0a8f1-550">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-550">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0a8f1-551">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-551">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0a8f1-552">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-552">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0a8f1-553">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-553">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0a8f1-554">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-554">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0a8f1-555">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-555">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0a8f1-556">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-556">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0a8f1-557">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-557">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0a8f1-558">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="0a8f1-558">**First request:**</span></span>

<span data-ttu-id="0a8f1-559">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-559">Controller operations:</span></span>

<span data-ttu-id="0a8f1-560">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0a8f1-560">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0a8f1-561">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a8f1-561">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a8f1-562">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-562">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-563">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-563">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-564">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-564">`OperationService` operations:</span></span>

<span data-ttu-id="0a8f1-565">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0a8f1-565">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0a8f1-566">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0a8f1-566">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0a8f1-567">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-567">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-568">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-568">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-569">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="0a8f1-569">**Second request:**</span></span>

<span data-ttu-id="0a8f1-570">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-570">Controller operations:</span></span>

<span data-ttu-id="0a8f1-571">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0a8f1-571">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0a8f1-572">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a8f1-572">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a8f1-573">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-573">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-574">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-574">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-575">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-575">`OperationService` operations:</span></span>

<span data-ttu-id="0a8f1-576">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0a8f1-576">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0a8f1-577">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0a8f1-577">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0a8f1-578">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0a8f1-578">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0a8f1-579">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0a8f1-579">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0a8f1-580">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-580">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0a8f1-581">暂时性  对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-581">*Transient* objects are always different.</span></span> <span data-ttu-id="0a8f1-582">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-582">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0a8f1-583">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-583">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0a8f1-584">作用域  对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-584">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0a8f1-585">单一实例  对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-585">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0a8f1-586">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-586">Call services from main</span></span>

<span data-ttu-id="0a8f1-587">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-587">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0a8f1-588">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-588">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0a8f1-589">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-589">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0a8f1-590">作用域验证</span><span class="sxs-lookup"><span data-stu-id="0a8f1-590">Scope validation</span></span>

<span data-ttu-id="0a8f1-591">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-591">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0a8f1-592">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-592">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0a8f1-593">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-593">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0a8f1-594">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-594">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0a8f1-595">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-595">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0a8f1-596">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-596">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0a8f1-597">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-597">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0a8f1-598">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-598">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0a8f1-599">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-599">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0a8f1-600">请求服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-600">Request Services</span></span>

<span data-ttu-id="0a8f1-601">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-601">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0a8f1-602">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-602">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0a8f1-603">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-603">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0a8f1-604">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-604">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0a8f1-605">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-605">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0a8f1-606">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-606">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0a8f1-607">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-607">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0a8f1-608">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-608">Design services for dependency injection</span></span>

<span data-ttu-id="0a8f1-609">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-609">Best practices are to:</span></span>

* <span data-ttu-id="0a8f1-610">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-610">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0a8f1-611">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-611">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0a8f1-612">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-612">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0a8f1-613">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-613">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0a8f1-614">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-614">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0a8f1-615">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-615">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0a8f1-616">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-616">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0a8f1-617">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-617">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0a8f1-618">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-618">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0a8f1-619">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-619">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0a8f1-620">服务处理</span><span class="sxs-lookup"><span data-stu-id="0a8f1-620">Disposal of services</span></span>

<span data-ttu-id="0a8f1-621">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-621">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0a8f1-622">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-622">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0a8f1-623">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-623">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0a8f1-624">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-624">In the following example:</span></span>

* <span data-ttu-id="0a8f1-625">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-625">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0a8f1-626">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-626">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0a8f1-627">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-627">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0a8f1-628">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-628">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="0a8f1-629">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="0a8f1-629">Default service container replacement</span></span>

<span data-ttu-id="0a8f1-630">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-630">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0a8f1-631">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-631">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0a8f1-632">属性注入</span><span class="sxs-lookup"><span data-stu-id="0a8f1-632">Property injection</span></span>
* <span data-ttu-id="0a8f1-633">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="0a8f1-633">Injection based on name</span></span>
* <span data-ttu-id="0a8f1-634">子容器</span><span class="sxs-lookup"><span data-stu-id="0a8f1-634">Child containers</span></span>
* <span data-ttu-id="0a8f1-635">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="0a8f1-635">Custom lifetime management</span></span>
* <span data-ttu-id="0a8f1-636">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="0a8f1-636">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0a8f1-637">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="0a8f1-637">Convention-based registration</span></span>

<span data-ttu-id="0a8f1-638">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-638">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0a8f1-639">Autofac</span><span class="sxs-lookup"><span data-stu-id="0a8f1-639">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0a8f1-640">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0a8f1-640">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a8f1-641">Grace</span><span class="sxs-lookup"><span data-stu-id="0a8f1-641">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0a8f1-642">LightInject</span><span class="sxs-lookup"><span data-stu-id="0a8f1-642">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0a8f1-643">Lamar</span><span class="sxs-lookup"><span data-stu-id="0a8f1-643">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0a8f1-644">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0a8f1-644">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0a8f1-645">Unity</span><span class="sxs-lookup"><span data-stu-id="0a8f1-645">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0a8f1-646">线程安全</span><span class="sxs-lookup"><span data-stu-id="0a8f1-646">Thread safety</span></span>

<span data-ttu-id="0a8f1-647">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-647">Create thread-safe singleton services.</span></span> <span data-ttu-id="0a8f1-648">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-648">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0a8f1-649">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-649">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0a8f1-650">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-650">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0a8f1-651">建议</span><span class="sxs-lookup"><span data-stu-id="0a8f1-651">Recommendations</span></span>

* <span data-ttu-id="0a8f1-652">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-652">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0a8f1-653">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-653">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0a8f1-654">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-654">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0a8f1-655">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-655">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0a8f1-656">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-656">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0a8f1-657">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-657">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0a8f1-658">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-658">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0a8f1-659">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-659">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0a8f1-660">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-660">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="0a8f1-661">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-661">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="0a8f1-662">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="0a8f1-662">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="0a8f1-663">**正确**：</span><span class="sxs-lookup"><span data-stu-id="0a8f1-663">**Correct**:</span></span>

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

  * <span data-ttu-id="0a8f1-664">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-664">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="0a8f1-665">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-665">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0a8f1-666">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-666">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0a8f1-667">例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-667">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0a8f1-668">DI 是静态/全局对象访问模式的替代方法  。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-668">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0a8f1-669">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="0a8f1-669">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a8f1-670">其他资源</span><span class="sxs-lookup"><span data-stu-id="0a8f1-670">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0a8f1-671">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="0a8f1-671">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="0a8f1-672">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="0a8f1-672">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="0a8f1-673">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0a8f1-673">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0a8f1-674">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="0a8f1-674">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
