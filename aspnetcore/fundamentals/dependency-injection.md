---
title: 在 ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 2074aa75029cf27922b43545ec18c0cd8a50eb02
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793345"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="b22b6-103">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="b22b6-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="b22b6-104">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="b22b6-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b22b6-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="b22b6-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b22b6-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b22b6-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b22b6-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b22b6-108">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="b22b6-108">Overview of dependency injection</span></span>

<span data-ttu-id="b22b6-109">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="b22b6-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b22b6-110">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="b22b6-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b22b6-111">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b22b6-112">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="b22b6-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b22b6-113">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b22b6-114">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="b22b6-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b22b6-115">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b22b6-116">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="b22b6-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b22b6-117">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="b22b6-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b22b6-118">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="b22b6-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b22b6-119">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b22b6-120">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="b22b6-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b22b6-121">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="b22b6-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b22b6-122">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b22b6-123">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b22b6-124">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="b22b6-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b22b6-125">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="b22b6-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b22b6-126">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="b22b6-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b22b6-127">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="b22b6-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b22b6-128">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="b22b6-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b22b6-129">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b22b6-130">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="b22b6-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b22b6-131">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b22b6-132">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b22b6-133">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="b22b6-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b22b6-134">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b22b6-135">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="b22b6-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b22b6-136">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b22b6-137">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="b22b6-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b22b6-138">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b22b6-139">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b22b6-140">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b22b6-141">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b22b6-142">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b22b6-143">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="b22b6-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b22b6-144">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="b22b6-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b22b6-145">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="b22b6-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b22b6-146">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b22b6-147">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b22b6-148">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="b22b6-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b22b6-149">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-149">Services injected into Startup</span></span>

<span data-ttu-id="b22b6-150">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="b22b6-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b22b6-151">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="b22b6-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b22b6-152">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b22b6-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-153">Framework-provided services</span></span>

<span data-ttu-id="b22b6-154">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="b22b6-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b22b6-155">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b22b6-156">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="b22b6-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b22b6-157">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b22b6-158">服务类型</span><span class="sxs-lookup"><span data-stu-id="b22b6-158">Service Type</span></span> | <span data-ttu-id="b22b6-159">生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b22b6-160">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="b22b6-161">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="b22b6-162">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b22b6-163">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b22b6-164">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b22b6-165">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b22b6-166">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b22b6-167">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b22b6-168">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b22b6-169">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b22b6-170">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b22b6-171">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b22b6-172">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b22b6-173">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b22b6-174">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-174">Register additional services with extension methods</span></span>

<span data-ttu-id="b22b6-175">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b22b6-176">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b22b6-177">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b22b6-178">服务生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-178">Service lifetimes</span></span>

<span data-ttu-id="b22b6-179">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b22b6-180">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="b22b6-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b22b6-181">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-181">Transient</span></span>

<span data-ttu-id="b22b6-182">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b22b6-183">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="b22b6-184">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="b22b6-185">范围内</span><span class="sxs-lookup"><span data-stu-id="b22b6-185">Scoped</span></span>

<span data-ttu-id="b22b6-186">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="b22b6-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="b22b6-187">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="b22b6-188">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b22b6-189">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="b22b6-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b22b6-190">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b22b6-191">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-191">Singleton</span></span>

<span data-ttu-id="b22b6-192">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b22b6-193">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b22b6-194">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b22b6-195">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="b22b6-196">在处理请求的应用中，在应用关闭，释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="b22b6-197">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="b22b6-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b22b6-198">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="b22b6-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b22b6-199">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="b22b6-199">Service registration methods</span></span>

<span data-ttu-id="b22b6-200">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="b22b6-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b22b6-201">方法</span><span class="sxs-lookup"><span data-stu-id="b22b6-201">Method</span></span> | <span data-ttu-id="b22b6-202">自动</span><span class="sxs-lookup"><span data-stu-id="b22b6-202">Automatic</span></span><br><span data-ttu-id="b22b6-203">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="b22b6-203">object</span></span><br><span data-ttu-id="b22b6-204">处置</span><span class="sxs-lookup"><span data-stu-id="b22b6-204">disposal</span></span> | <span data-ttu-id="b22b6-205">多种</span><span class="sxs-lookup"><span data-stu-id="b22b6-205">Multiple</span></span><br><span data-ttu-id="b22b6-206">实现</span><span class="sxs-lookup"><span data-stu-id="b22b6-206">implementations</span></span> | <span data-ttu-id="b22b6-207">传递参数</span><span class="sxs-lookup"><span data-stu-id="b22b6-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b22b6-208">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b22b6-209">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-209">Yes</span></span> | <span data-ttu-id="b22b6-210">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-210">Yes</span></span> | <span data-ttu-id="b22b6-211">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b22b6-212">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b22b6-213">是</span><span class="sxs-lookup"><span data-stu-id="b22b6-213">Yes</span></span> | <span data-ttu-id="b22b6-214">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-214">Yes</span></span> | <span data-ttu-id="b22b6-215">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b22b6-216">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b22b6-217">是</span><span class="sxs-lookup"><span data-stu-id="b22b6-217">Yes</span></span> | <span data-ttu-id="b22b6-218">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-218">No</span></span> | <span data-ttu-id="b22b6-219">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b22b6-220">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b22b6-221">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-221">No</span></span> | <span data-ttu-id="b22b6-222">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-222">Yes</span></span> | <span data-ttu-id="b22b6-223">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b22b6-224">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b22b6-225">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-225">No</span></span> | <span data-ttu-id="b22b6-226">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-226">No</span></span> | <span data-ttu-id="b22b6-227">是</span><span class="sxs-lookup"><span data-stu-id="b22b6-227">Yes</span></span> |

<span data-ttu-id="b22b6-228">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="b22b6-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b22b6-229">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b22b6-230">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b22b6-231">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b22b6-232">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="b22b6-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b22b6-233">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="b22b6-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b22b6-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b22b6-235">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="b22b6-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b22b6-236">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b22b6-237">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="b22b6-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b22b6-238">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b22b6-239">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b22b6-240">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="b22b6-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b22b6-241">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="b22b6-241">Constructor injection behavior</span></span>

<span data-ttu-id="b22b6-242">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="b22b6-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b22b6-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="b22b6-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b22b6-244">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="b22b6-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b22b6-245">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="b22b6-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b22b6-246">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="b22b6-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b22b6-247">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="b22b6-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b22b6-248">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="b22b6-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b22b6-249">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="b22b6-249">Entity Framework contexts</span></span>

<span data-ttu-id="b22b6-250">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="b22b6-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b22b6-251">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="b22b6-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b22b6-252">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="b22b6-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b22b6-253">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="b22b6-253">Lifetime and registration options</span></span>

<span data-ttu-id="b22b6-254">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="b22b6-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b22b6-255">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b22b6-256">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="b22b6-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b22b6-257">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="b22b6-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b22b6-258">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="b22b6-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b22b6-259">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b22b6-260">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="b22b6-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b22b6-261">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b22b6-262">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b22b6-263">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="b22b6-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b22b6-264">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="b22b6-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b22b6-265">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="b22b6-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b22b6-266">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="b22b6-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b22b6-267">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b22b6-268">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b22b6-269">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b22b6-270">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b22b6-271">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="b22b6-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b22b6-272">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="b22b6-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b22b6-273">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="b22b6-273">**First request:**</span></span>

<span data-ttu-id="b22b6-274">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-274">Controller operations:</span></span>

<span data-ttu-id="b22b6-275">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b22b6-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b22b6-276">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b22b6-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b22b6-277">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-278">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-279">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-279">`OperationService` operations:</span></span>

<span data-ttu-id="b22b6-280">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b22b6-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b22b6-281">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b22b6-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b22b6-282">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-283">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-284">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="b22b6-284">**Second request:**</span></span>

<span data-ttu-id="b22b6-285">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-285">Controller operations:</span></span>

<span data-ttu-id="b22b6-286">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b22b6-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b22b6-287">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b22b6-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b22b6-288">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-289">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-290">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-290">`OperationService` operations:</span></span>

<span data-ttu-id="b22b6-291">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b22b6-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b22b6-292">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b22b6-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b22b6-293">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-294">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-295">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="b22b6-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b22b6-296">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="b22b6-296">*Transient* objects are always different.</span></span> <span data-ttu-id="b22b6-297">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b22b6-298">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b22b6-299">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b22b6-300">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b22b6-301">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-301">Call services from main</span></span>

<span data-ttu-id="b22b6-302">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b22b6-303">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b22b6-304">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="b22b6-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b22b6-305">作用域验证</span><span class="sxs-lookup"><span data-stu-id="b22b6-305">Scope validation</span></span>

<span data-ttu-id="b22b6-306">如果应用正在开发环境中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="b22b6-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b22b6-307">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b22b6-308">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b22b6-309">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="b22b6-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b22b6-310">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="b22b6-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b22b6-311">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="b22b6-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b22b6-312">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="b22b6-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b22b6-313">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="b22b6-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b22b6-314">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b22b6-315">请求服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-315">Request Services</span></span>

<span data-ttu-id="b22b6-316">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="b22b6-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b22b6-317">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b22b6-318">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="b22b6-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b22b6-319">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="b22b6-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b22b6-320">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="b22b6-321">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="b22b6-321">This yields classes that are easier to test.</span></span>

<span data-ttu-id="b22b6-322">ASP.NET Core 为每个请求创建一个范围，`RequestServices` 公开范围内服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="b22b6-322">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="b22b6-323">只要请求处于活动状态，所有范围内的服务都有效。</span><span class="sxs-lookup"><span data-stu-id="b22b6-323">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="b22b6-324">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="b22b6-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b22b6-325">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-325">Design services for dependency injection</span></span>

<span data-ttu-id="b22b6-326">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="b22b6-326">Best practices are to:</span></span>

* <span data-ttu-id="b22b6-327">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-327">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b22b6-328">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="b22b6-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b22b6-329">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="b22b6-329">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b22b6-330">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b22b6-331">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="b22b6-331">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b22b6-332">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="b22b6-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b22b6-333">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b22b6-334">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b22b6-335">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="b22b6-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b22b6-336">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="b22b6-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b22b6-337">服务处理</span><span class="sxs-lookup"><span data-stu-id="b22b6-337">Disposal of services</span></span>

<span data-ttu-id="b22b6-338">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b22b6-339">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b22b6-340">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="b22b6-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b22b6-341">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="b22b6-341">In the following example:</span></span>

* <span data-ttu-id="b22b6-342">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-342">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b22b6-343">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-343">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b22b6-344">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-344">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b22b6-345">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="b22b6-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="b22b6-346">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="b22b6-346">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="b22b6-347">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-347">Transient, limited lifetime</span></span>

<span data-ttu-id="b22b6-348">**方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-348">**Scenario**</span></span>

<span data-ttu-id="b22b6-349">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="b22b6-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="b22b6-350">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-350">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="b22b6-351">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-351">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="b22b6-352">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-352">**Solution**</span></span>

<span data-ttu-id="b22b6-353">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-353">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="b22b6-354">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="b22b6-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="b22b6-355">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="b22b6-355">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="b22b6-356">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="b22b6-357">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="b22b6-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="b22b6-358">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-358">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="b22b6-359">**方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-359">**Scenario**</span></span>

<span data-ttu-id="b22b6-360">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="b22b6-361">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-361">**Solution**</span></span>

<span data-ttu-id="b22b6-362">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="b22b6-363">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="b22b6-364">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="b22b6-365">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="b22b6-365">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="b22b6-366">通用准则</span><span class="sxs-lookup"><span data-stu-id="b22b6-366">General Guidelines</span></span>

* <span data-ttu-id="b22b6-367">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="b22b6-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="b22b6-368">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="b22b6-368">Use the factory pattern instead.</span></span>
* <span data-ttu-id="b22b6-369">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="b22b6-370">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="b22b6-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="b22b6-371">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="b22b6-372"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="b22b6-373">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-373">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="b22b6-374">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="b22b6-375">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="b22b6-375">Default service container replacement</span></span>

<span data-ttu-id="b22b6-376">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="b22b6-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b22b6-377">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="b22b6-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b22b6-378">属性注入</span><span class="sxs-lookup"><span data-stu-id="b22b6-378">Property injection</span></span>
* <span data-ttu-id="b22b6-379">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="b22b6-379">Injection based on name</span></span>
* <span data-ttu-id="b22b6-380">子容器</span><span class="sxs-lookup"><span data-stu-id="b22b6-380">Child containers</span></span>
* <span data-ttu-id="b22b6-381">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="b22b6-381">Custom lifetime management</span></span>
* <span data-ttu-id="b22b6-382">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="b22b6-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b22b6-383">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="b22b6-383">Convention-based registration</span></span>

<span data-ttu-id="b22b6-384">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="b22b6-384">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b22b6-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="b22b6-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b22b6-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="b22b6-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b22b6-387">Grace</span><span class="sxs-lookup"><span data-stu-id="b22b6-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b22b6-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="b22b6-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b22b6-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="b22b6-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b22b6-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="b22b6-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b22b6-391">Unity</span><span class="sxs-lookup"><span data-stu-id="b22b6-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b22b6-392">线程安全</span><span class="sxs-lookup"><span data-stu-id="b22b6-392">Thread safety</span></span>

<span data-ttu-id="b22b6-393">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="b22b6-394">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="b22b6-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b22b6-395">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b22b6-396">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="b22b6-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b22b6-397">建议</span><span class="sxs-lookup"><span data-stu-id="b22b6-397">Recommendations</span></span>

* <span data-ttu-id="b22b6-398">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="b22b6-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b22b6-399">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b22b6-400">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="b22b6-400">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b22b6-401">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="b22b6-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b22b6-402">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b22b6-403">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="b22b6-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b22b6-404">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="b22b6-404">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b22b6-405">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b22b6-406">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="b22b6-406">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="b22b6-407">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="b22b6-408">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="b22b6-408">**Incorrect:**</span></span>

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

  <span data-ttu-id="b22b6-409">**正确**：</span><span class="sxs-lookup"><span data-stu-id="b22b6-409">**Correct**:</span></span>

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

* <span data-ttu-id="b22b6-410">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="b22b6-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="b22b6-411">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="b22b6-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="b22b6-412">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b22b6-413">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="b22b6-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b22b6-414">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="b22b6-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b22b6-415">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-415">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b22b6-416">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="b22b6-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="b22b6-417">DI 中多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="b22b6-417">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="b22b6-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="b22b6-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="b22b6-419">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="b22b6-420">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b22b6-421">其他资源</span><span class="sxs-lookup"><span data-stu-id="b22b6-421">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b22b6-422">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="b22b6-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="b22b6-423">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="b22b6-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="b22b6-424">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="b22b6-424">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="b22b6-425">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="b22b6-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b22b6-426">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-426">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b22b6-427">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="b22b6-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b22b6-428">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b22b6-429">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b22b6-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b22b6-430">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="b22b6-430">Overview of dependency injection</span></span>

<span data-ttu-id="b22b6-431">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="b22b6-431">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b22b6-432">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="b22b6-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b22b6-433">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b22b6-434">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="b22b6-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b22b6-435">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b22b6-436">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="b22b6-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b22b6-437">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b22b6-438">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="b22b6-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b22b6-439">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="b22b6-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b22b6-440">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="b22b6-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b22b6-441">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b22b6-442">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="b22b6-442">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b22b6-443">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="b22b6-443">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b22b6-444">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-444">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b22b6-445">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b22b6-446">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="b22b6-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b22b6-447">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="b22b6-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b22b6-448">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="b22b6-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b22b6-449">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="b22b6-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b22b6-450">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="b22b6-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b22b6-451">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b22b6-452">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="b22b6-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b22b6-453">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-453">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b22b6-454">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b22b6-455">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="b22b6-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b22b6-456">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b22b6-457">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="b22b6-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b22b6-458">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b22b6-459">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="b22b6-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b22b6-460">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b22b6-461">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b22b6-462">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b22b6-463">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b22b6-464">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b22b6-465">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="b22b6-465">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b22b6-466">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="b22b6-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b22b6-467">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="b22b6-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b22b6-468">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b22b6-469">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-469">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b22b6-470">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="b22b6-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b22b6-471">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-471">Services injected into Startup</span></span>

<span data-ttu-id="b22b6-472">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="b22b6-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b22b6-473">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="b22b6-473">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b22b6-474">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-474">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b22b6-475">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-475">Framework-provided services</span></span>

<span data-ttu-id="b22b6-476">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="b22b6-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b22b6-477">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b22b6-478">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="b22b6-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b22b6-479">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-479">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b22b6-480">服务类型</span><span class="sxs-lookup"><span data-stu-id="b22b6-480">Service Type</span></span> | <span data-ttu-id="b22b6-481">生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-481">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b22b6-482">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="b22b6-483">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="b22b6-484">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-484">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b22b6-485">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b22b6-486">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-486">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b22b6-487">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-487">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b22b6-488">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-488">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b22b6-489">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b22b6-490">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-490">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b22b6-491">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-491">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b22b6-492">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-492">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b22b6-493">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-493">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b22b6-494">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-494">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b22b6-495">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-495">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b22b6-496">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-496">Register additional services with extension methods</span></span>

<span data-ttu-id="b22b6-497">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b22b6-498">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b22b6-499">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b22b6-500">服务生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-500">Service lifetimes</span></span>

<span data-ttu-id="b22b6-501">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-501">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b22b6-502">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="b22b6-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b22b6-503">暂时</span><span class="sxs-lookup"><span data-stu-id="b22b6-503">Transient</span></span>

<span data-ttu-id="b22b6-504">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b22b6-505">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-505">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="b22b6-506">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-506">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="b22b6-507">范围内</span><span class="sxs-lookup"><span data-stu-id="b22b6-507">Scoped</span></span>

<span data-ttu-id="b22b6-508">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="b22b6-508">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="b22b6-509">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-509">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="b22b6-510">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-510">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b22b6-511">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="b22b6-511">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b22b6-512">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-512">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b22b6-513">单例</span><span class="sxs-lookup"><span data-stu-id="b22b6-513">Singleton</span></span>

<span data-ttu-id="b22b6-514">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-514">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b22b6-515">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-515">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b22b6-516">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-516">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b22b6-517">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-517">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="b22b6-518">在处理请求的应用中，在应用关闭，释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-518">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="b22b6-519">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="b22b6-519">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b22b6-520">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="b22b6-520">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b22b6-521">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="b22b6-521">Service registration methods</span></span>

<span data-ttu-id="b22b6-522">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="b22b6-522">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b22b6-523">方法</span><span class="sxs-lookup"><span data-stu-id="b22b6-523">Method</span></span> | <span data-ttu-id="b22b6-524">自动</span><span class="sxs-lookup"><span data-stu-id="b22b6-524">Automatic</span></span><br><span data-ttu-id="b22b6-525">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="b22b6-525">object</span></span><br><span data-ttu-id="b22b6-526">处置</span><span class="sxs-lookup"><span data-stu-id="b22b6-526">disposal</span></span> | <span data-ttu-id="b22b6-527">多种</span><span class="sxs-lookup"><span data-stu-id="b22b6-527">Multiple</span></span><br><span data-ttu-id="b22b6-528">实现</span><span class="sxs-lookup"><span data-stu-id="b22b6-528">implementations</span></span> | <span data-ttu-id="b22b6-529">传递参数</span><span class="sxs-lookup"><span data-stu-id="b22b6-529">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b22b6-530">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-530">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b22b6-531">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-531">Yes</span></span> | <span data-ttu-id="b22b6-532">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-532">Yes</span></span> | <span data-ttu-id="b22b6-533">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-533">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b22b6-534">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b22b6-535">是</span><span class="sxs-lookup"><span data-stu-id="b22b6-535">Yes</span></span> | <span data-ttu-id="b22b6-536">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-536">Yes</span></span> | <span data-ttu-id="b22b6-537">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-537">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b22b6-538">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-538">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b22b6-539">是</span><span class="sxs-lookup"><span data-stu-id="b22b6-539">Yes</span></span> | <span data-ttu-id="b22b6-540">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-540">No</span></span> | <span data-ttu-id="b22b6-541">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-541">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b22b6-542">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-542">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b22b6-543">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-543">No</span></span> | <span data-ttu-id="b22b6-544">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-544">Yes</span></span> | <span data-ttu-id="b22b6-545">“是”</span><span class="sxs-lookup"><span data-stu-id="b22b6-545">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b22b6-546">示例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-546">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b22b6-547">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-547">No</span></span> | <span data-ttu-id="b22b6-548">否</span><span class="sxs-lookup"><span data-stu-id="b22b6-548">No</span></span> | <span data-ttu-id="b22b6-549">是</span><span class="sxs-lookup"><span data-stu-id="b22b6-549">Yes</span></span> |

<span data-ttu-id="b22b6-550">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="b22b6-550">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b22b6-551">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-551">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b22b6-552">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-552">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b22b6-553">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-553">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b22b6-554">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="b22b6-554">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b22b6-555">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="b22b6-555">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b22b6-556">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-556">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b22b6-557">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="b22b6-557">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b22b6-558">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-558">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b22b6-559">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="b22b6-559">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b22b6-560">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-560">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b22b6-561">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-561">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b22b6-562">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="b22b6-562">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b22b6-563">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="b22b6-563">Constructor injection behavior</span></span>

<span data-ttu-id="b22b6-564">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="b22b6-564">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b22b6-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="b22b6-565"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b22b6-566">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="b22b6-566">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b22b6-567">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="b22b6-567">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b22b6-568">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="b22b6-568">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b22b6-569">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="b22b6-569">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b22b6-570">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="b22b6-570">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b22b6-571">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="b22b6-571">Entity Framework contexts</span></span>

<span data-ttu-id="b22b6-572">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="b22b6-572">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b22b6-573">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="b22b6-573">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b22b6-574">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="b22b6-574">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b22b6-575">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="b22b6-575">Lifetime and registration options</span></span>

<span data-ttu-id="b22b6-576">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="b22b6-576">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b22b6-577">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-577">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b22b6-578">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="b22b6-578">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b22b6-579">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="b22b6-579">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b22b6-580">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="b22b6-580">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b22b6-581">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-581">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b22b6-582">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="b22b6-582">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b22b6-583">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-583">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b22b6-584">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-584">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b22b6-585">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="b22b6-585">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b22b6-586">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="b22b6-586">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b22b6-587">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="b22b6-587">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b22b6-588">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="b22b6-588">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b22b6-589">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-589">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b22b6-590">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-590">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b22b6-591">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-591">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b22b6-592">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="b22b6-592">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b22b6-593">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="b22b6-593">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b22b6-594">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="b22b6-594">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b22b6-595">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="b22b6-595">**First request:**</span></span>

<span data-ttu-id="b22b6-596">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-596">Controller operations:</span></span>

<span data-ttu-id="b22b6-597">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b22b6-597">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b22b6-598">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b22b6-598">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b22b6-599">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-599">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-600">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-600">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-601">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-601">`OperationService` operations:</span></span>

<span data-ttu-id="b22b6-602">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b22b6-602">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b22b6-603">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b22b6-603">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b22b6-604">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-604">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-605">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-605">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-606">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="b22b6-606">**Second request:**</span></span>

<span data-ttu-id="b22b6-607">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-607">Controller operations:</span></span>

<span data-ttu-id="b22b6-608">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b22b6-608">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b22b6-609">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b22b6-609">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b22b6-610">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-610">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-611">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-611">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-612">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="b22b6-612">`OperationService` operations:</span></span>

<span data-ttu-id="b22b6-613">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b22b6-613">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b22b6-614">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b22b6-614">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b22b6-615">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b22b6-615">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b22b6-616">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b22b6-616">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b22b6-617">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="b22b6-617">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b22b6-618">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="b22b6-618">*Transient* objects are always different.</span></span> <span data-ttu-id="b22b6-619">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-619">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b22b6-620">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-620">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b22b6-621">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-621">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b22b6-622">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-622">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b22b6-623">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-623">Call services from main</span></span>

<span data-ttu-id="b22b6-624">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-624">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b22b6-625">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-625">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b22b6-626">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="b22b6-626">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b22b6-627">作用域验证</span><span class="sxs-lookup"><span data-stu-id="b22b6-627">Scope validation</span></span>

<span data-ttu-id="b22b6-628">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="b22b6-628">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b22b6-629">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-629">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b22b6-630">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-630">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b22b6-631">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="b22b6-631">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b22b6-632">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="b22b6-632">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b22b6-633">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="b22b6-633">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b22b6-634">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="b22b6-634">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b22b6-635">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="b22b6-635">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b22b6-636">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-636">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b22b6-637">请求服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-637">Request Services</span></span>

<span data-ttu-id="b22b6-638">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="b22b6-638">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b22b6-639">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-639">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b22b6-640">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="b22b6-640">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b22b6-641">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="b22b6-641">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b22b6-642">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-642">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="b22b6-643">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="b22b6-643">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="b22b6-644">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="b22b6-644">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b22b6-645">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-645">Design services for dependency injection</span></span>

<span data-ttu-id="b22b6-646">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="b22b6-646">Best practices are to:</span></span>

* <span data-ttu-id="b22b6-647">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-647">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b22b6-648">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="b22b6-648">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b22b6-649">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="b22b6-649">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b22b6-650">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-650">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b22b6-651">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="b22b6-651">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b22b6-652">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="b22b6-652">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b22b6-653">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-653">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b22b6-654">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="b22b6-654">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b22b6-655">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="b22b6-655">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b22b6-656">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="b22b6-656">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b22b6-657">服务处理</span><span class="sxs-lookup"><span data-stu-id="b22b6-657">Disposal of services</span></span>

<span data-ttu-id="b22b6-658">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-658">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b22b6-659">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-659">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b22b6-660">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="b22b6-660">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b22b6-661">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="b22b6-661">In the following example:</span></span>

* <span data-ttu-id="b22b6-662">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-662">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b22b6-663">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-663">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b22b6-664">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-664">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b22b6-665">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="b22b6-665">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="b22b6-666">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="b22b6-666">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="b22b6-667">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-667">Transient, limited lifetime</span></span>

<span data-ttu-id="b22b6-668">**方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-668">**Scenario**</span></span>

<span data-ttu-id="b22b6-669">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="b22b6-669">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="b22b6-670">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-670">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="b22b6-671">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-671">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="b22b6-672">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-672">**Solution**</span></span>

<span data-ttu-id="b22b6-673">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-673">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="b22b6-674">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="b22b6-674">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="b22b6-675">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="b22b6-675">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="b22b6-676">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-676">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="b22b6-677">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="b22b6-677">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="b22b6-678">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="b22b6-678">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="b22b6-679">**方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-679">**Scenario**</span></span>

<span data-ttu-id="b22b6-680">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-680">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="b22b6-681">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="b22b6-681">**Solution**</span></span>

<span data-ttu-id="b22b6-682">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-682">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="b22b6-683">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-683">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="b22b6-684">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-684">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="b22b6-685">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="b22b6-685">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="b22b6-686">通用准则</span><span class="sxs-lookup"><span data-stu-id="b22b6-686">General Guidelines</span></span>

* <span data-ttu-id="b22b6-687">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="b22b6-687">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="b22b6-688">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="b22b6-688">Use the factory pattern instead.</span></span>
* <span data-ttu-id="b22b6-689">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="b22b6-689">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="b22b6-690">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="b22b6-690">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="b22b6-691">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-691">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="b22b6-692"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="b22b6-692">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="b22b6-693">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="b22b6-693">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="b22b6-694">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="b22b6-694">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="b22b6-695">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="b22b6-695">Default service container replacement</span></span>

<span data-ttu-id="b22b6-696">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="b22b6-696">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b22b6-697">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="b22b6-697">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b22b6-698">属性注入</span><span class="sxs-lookup"><span data-stu-id="b22b6-698">Property injection</span></span>
* <span data-ttu-id="b22b6-699">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="b22b6-699">Injection based on name</span></span>
* <span data-ttu-id="b22b6-700">子容器</span><span class="sxs-lookup"><span data-stu-id="b22b6-700">Child containers</span></span>
* <span data-ttu-id="b22b6-701">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="b22b6-701">Custom lifetime management</span></span>
* <span data-ttu-id="b22b6-702">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="b22b6-702">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b22b6-703">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="b22b6-703">Convention-based registration</span></span>

<span data-ttu-id="b22b6-704">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="b22b6-704">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b22b6-705">Autofac</span><span class="sxs-lookup"><span data-stu-id="b22b6-705">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b22b6-706">DryIoc</span><span class="sxs-lookup"><span data-stu-id="b22b6-706">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b22b6-707">Grace</span><span class="sxs-lookup"><span data-stu-id="b22b6-707">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b22b6-708">LightInject</span><span class="sxs-lookup"><span data-stu-id="b22b6-708">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b22b6-709">Lamar</span><span class="sxs-lookup"><span data-stu-id="b22b6-709">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b22b6-710">Stashbox</span><span class="sxs-lookup"><span data-stu-id="b22b6-710">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b22b6-711">Unity</span><span class="sxs-lookup"><span data-stu-id="b22b6-711">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b22b6-712">线程安全</span><span class="sxs-lookup"><span data-stu-id="b22b6-712">Thread safety</span></span>

<span data-ttu-id="b22b6-713">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="b22b6-713">Create thread-safe singleton services.</span></span> <span data-ttu-id="b22b6-714">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="b22b6-714">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b22b6-715">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="b22b6-715">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b22b6-716">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="b22b6-716">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b22b6-717">建议</span><span class="sxs-lookup"><span data-stu-id="b22b6-717">Recommendations</span></span>

* <span data-ttu-id="b22b6-718">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="b22b6-718">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b22b6-719">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-719">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b22b6-720">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="b22b6-720">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b22b6-721">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="b22b6-721">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b22b6-722">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="b22b6-722">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b22b6-723">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="b22b6-723">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b22b6-724">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="b22b6-724">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b22b6-725">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-725">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b22b6-726">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="b22b6-726">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="b22b6-727">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="b22b6-727">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="b22b6-728">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="b22b6-728">**Incorrect:**</span></span>

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

    <span data-ttu-id="b22b6-729">**正确**：</span><span class="sxs-lookup"><span data-stu-id="b22b6-729">**Correct**:</span></span>

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

  * <span data-ttu-id="b22b6-730">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="b22b6-730">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="b22b6-731">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="b22b6-731">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b22b6-732">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="b22b6-732">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b22b6-733">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="b22b6-733">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b22b6-734">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="b22b6-734">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b22b6-735">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="b22b6-735">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b22b6-736">其他资源</span><span class="sxs-lookup"><span data-stu-id="b22b6-736">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b22b6-737">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="b22b6-737">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="b22b6-738">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="b22b6-738">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="b22b6-739">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="b22b6-739">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="b22b6-740">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="b22b6-740">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b22b6-741">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="b22b6-741">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
