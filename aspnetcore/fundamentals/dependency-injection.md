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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 34ed08a5b49b56fd37628032ac73fe03a34448e6
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240847"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="f5b06-103">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="f5b06-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="f5b06-104">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="f5b06-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5b06-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="f5b06-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f5b06-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f5b06-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f5b06-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f5b06-108">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="f5b06-108">Overview of dependency injection</span></span>

<span data-ttu-id="f5b06-109">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="f5b06-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f5b06-110">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="f5b06-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="f5b06-111">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f5b06-112">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="f5b06-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="f5b06-113">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f5b06-114">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="f5b06-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f5b06-115">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f5b06-116">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="f5b06-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f5b06-117">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="f5b06-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f5b06-118">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="f5b06-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f5b06-119">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f5b06-120">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="f5b06-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f5b06-121">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="f5b06-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f5b06-122">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f5b06-123">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f5b06-124">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="f5b06-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f5b06-125">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="f5b06-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f5b06-126">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="f5b06-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f5b06-127">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="f5b06-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f5b06-128">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="f5b06-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f5b06-129">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="f5b06-130">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="f5b06-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f5b06-131">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f5b06-132">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f5b06-133">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="f5b06-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f5b06-134">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f5b06-135">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="f5b06-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5b06-136">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f5b06-137">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="f5b06-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="f5b06-138">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f5b06-139">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f5b06-140">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="f5b06-141">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f5b06-142">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f5b06-143">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="f5b06-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f5b06-144">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="f5b06-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f5b06-145">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="f5b06-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f5b06-146">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f5b06-147">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f5b06-148">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="f5b06-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="f5b06-149">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-149">Services injected into Startup</span></span>

<span data-ttu-id="f5b06-150">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="f5b06-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f5b06-151">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="f5b06-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="f5b06-152">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f5b06-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-153">Framework-provided services</span></span>

<span data-ttu-id="f5b06-154">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="f5b06-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f5b06-155">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f5b06-156">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="f5b06-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="f5b06-157">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="f5b06-158">服务类型</span><span class="sxs-lookup"><span data-stu-id="f5b06-158">Service Type</span></span> | <span data-ttu-id="f5b06-159">生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f5b06-160">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="f5b06-161">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="f5b06-162">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f5b06-163">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f5b06-164">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f5b06-165">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f5b06-166">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f5b06-167">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f5b06-168">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f5b06-169">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f5b06-170">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f5b06-171">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f5b06-172">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f5b06-173">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="f5b06-174">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-174">Register additional services with extension methods</span></span>

<span data-ttu-id="f5b06-175">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f5b06-176">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="f5b06-177">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f5b06-178">服务生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-178">Service lifetimes</span></span>

<span data-ttu-id="f5b06-179">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f5b06-180">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="f5b06-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="f5b06-181">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-181">Transient</span></span>

<span data-ttu-id="f5b06-182">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="f5b06-183">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="f5b06-184">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f5b06-185">范围内</span><span class="sxs-lookup"><span data-stu-id="f5b06-185">Scoped</span></span>

<span data-ttu-id="f5b06-186">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="f5b06-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="f5b06-187">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="f5b06-188">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f5b06-189">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="f5b06-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f5b06-190">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f5b06-191">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-191">Singleton</span></span>

<span data-ttu-id="f5b06-192">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f5b06-193">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f5b06-194">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f5b06-195">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="f5b06-196">在处理请求的应用中，在应用关闭，释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="f5b06-197">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="f5b06-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f5b06-198">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="f5b06-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f5b06-199">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="f5b06-199">Service registration methods</span></span>

<span data-ttu-id="f5b06-200">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="f5b06-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="f5b06-201">方法</span><span class="sxs-lookup"><span data-stu-id="f5b06-201">Method</span></span> | <span data-ttu-id="f5b06-202">自动</span><span class="sxs-lookup"><span data-stu-id="f5b06-202">Automatic</span></span><br><span data-ttu-id="f5b06-203">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="f5b06-203">object</span></span><br><span data-ttu-id="f5b06-204">处置</span><span class="sxs-lookup"><span data-stu-id="f5b06-204">disposal</span></span> | <span data-ttu-id="f5b06-205">多种</span><span class="sxs-lookup"><span data-stu-id="f5b06-205">Multiple</span></span><br><span data-ttu-id="f5b06-206">实现</span><span class="sxs-lookup"><span data-stu-id="f5b06-206">implementations</span></span> | <span data-ttu-id="f5b06-207">传递参数</span><span class="sxs-lookup"><span data-stu-id="f5b06-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f5b06-208">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="f5b06-209">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-209">Yes</span></span> | <span data-ttu-id="f5b06-210">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-210">Yes</span></span> | <span data-ttu-id="f5b06-211">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f5b06-212">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="f5b06-213">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-213">Yes</span></span> | <span data-ttu-id="f5b06-214">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-214">Yes</span></span> | <span data-ttu-id="f5b06-215">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f5b06-216">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="f5b06-217">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-217">Yes</span></span> | <span data-ttu-id="f5b06-218">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-218">No</span></span> | <span data-ttu-id="f5b06-219">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f5b06-220">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="f5b06-221">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-221">No</span></span> | <span data-ttu-id="f5b06-222">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-222">Yes</span></span> | <span data-ttu-id="f5b06-223">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f5b06-224">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="f5b06-225">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-225">No</span></span> | <span data-ttu-id="f5b06-226">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-226">No</span></span> | <span data-ttu-id="f5b06-227">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-227">Yes</span></span> |

<span data-ttu-id="f5b06-228">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="f5b06-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f5b06-229">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f5b06-230">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f5b06-231">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="f5b06-232">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="f5b06-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f5b06-233">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="f5b06-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="f5b06-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f5b06-235">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="f5b06-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f5b06-236">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f5b06-237">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="f5b06-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="f5b06-238">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="f5b06-239">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="f5b06-240">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="f5b06-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f5b06-241">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="f5b06-241">Constructor injection behavior</span></span>

<span data-ttu-id="f5b06-242">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="f5b06-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f5b06-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="f5b06-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f5b06-244">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="f5b06-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f5b06-245">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="f5b06-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f5b06-246">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="f5b06-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f5b06-247">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="f5b06-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f5b06-248">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="f5b06-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f5b06-249">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="f5b06-249">Entity Framework contexts</span></span>

<span data-ttu-id="f5b06-250">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="f5b06-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f5b06-251">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="f5b06-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="f5b06-252">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="f5b06-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f5b06-253">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="f5b06-253">Lifetime and registration options</span></span>

<span data-ttu-id="f5b06-254">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="f5b06-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f5b06-255">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f5b06-256">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="f5b06-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f5b06-257">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="f5b06-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="f5b06-258">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="f5b06-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f5b06-259">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f5b06-260">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="f5b06-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f5b06-261">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f5b06-262">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f5b06-263">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="f5b06-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="f5b06-264">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="f5b06-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f5b06-265">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="f5b06-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="f5b06-266">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="f5b06-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="f5b06-267">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f5b06-268">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="f5b06-269">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f5b06-270">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f5b06-271">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="f5b06-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="f5b06-272">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="f5b06-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f5b06-273">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="f5b06-273">**First request:**</span></span>

<span data-ttu-id="f5b06-274">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-274">Controller operations:</span></span>

<span data-ttu-id="f5b06-275">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f5b06-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f5b06-276">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f5b06-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f5b06-277">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-278">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-279">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-279">`OperationService` operations:</span></span>

<span data-ttu-id="f5b06-280">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f5b06-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f5b06-281">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f5b06-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f5b06-282">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-283">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-284">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="f5b06-284">**Second request:**</span></span>

<span data-ttu-id="f5b06-285">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-285">Controller operations:</span></span>

<span data-ttu-id="f5b06-286">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f5b06-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f5b06-287">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f5b06-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f5b06-288">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-289">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-290">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-290">`OperationService` operations:</span></span>

<span data-ttu-id="f5b06-291">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f5b06-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f5b06-292">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f5b06-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f5b06-293">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-294">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-295">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="f5b06-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f5b06-296">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="f5b06-296">*Transient* objects are always different.</span></span> <span data-ttu-id="f5b06-297">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="f5b06-298">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="f5b06-299">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="f5b06-300">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f5b06-301">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-301">Call services from main</span></span>

<span data-ttu-id="f5b06-302">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f5b06-303">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f5b06-304">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="f5b06-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="f5b06-305">作用域验证</span><span class="sxs-lookup"><span data-stu-id="f5b06-305">Scope validation</span></span>

<span data-ttu-id="f5b06-306">如果应用正在开发环境中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="f5b06-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f5b06-307">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f5b06-308">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f5b06-309">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="f5b06-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="f5b06-310">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="f5b06-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f5b06-311">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="f5b06-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f5b06-312">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="f5b06-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f5b06-313">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="f5b06-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f5b06-314">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="f5b06-315">请求服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-315">Request Services</span></span>

<span data-ttu-id="f5b06-316">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="f5b06-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="f5b06-317">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f5b06-318">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="f5b06-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f5b06-319">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="f5b06-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f5b06-320">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="f5b06-321">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="f5b06-321">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="f5b06-322">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="f5b06-322">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f5b06-323">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-323">Design services for dependency injection</span></span>

<span data-ttu-id="f5b06-324">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="f5b06-324">Best practices are to:</span></span>

* <span data-ttu-id="f5b06-325">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-325">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f5b06-326">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="f5b06-326">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f5b06-327">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="f5b06-327">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="f5b06-328">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-328">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f5b06-329">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="f5b06-329">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f5b06-330">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="f5b06-330">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f5b06-331">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-331">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f5b06-332">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-332">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f5b06-333">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="f5b06-333">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f5b06-334">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="f5b06-334">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f5b06-335">服务处理</span><span class="sxs-lookup"><span data-stu-id="f5b06-335">Disposal of services</span></span>

<span data-ttu-id="f5b06-336">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-336">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f5b06-337">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-337">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="f5b06-338">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="f5b06-338">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="f5b06-339">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f5b06-339">In the following example:</span></span>

* <span data-ttu-id="f5b06-340">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-340">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f5b06-341">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-341">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="f5b06-342">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-342">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f5b06-343">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="f5b06-343">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f5b06-344">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="f5b06-344">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f5b06-345">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-345">Transient, limited lifetime</span></span>

<span data-ttu-id="f5b06-346">**方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-346">**Scenario**</span></span>

<span data-ttu-id="f5b06-347">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="f5b06-347">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f5b06-348">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-348">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="f5b06-349">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-349">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f5b06-350">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-350">**Solution**</span></span>

<span data-ttu-id="f5b06-351">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-351">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f5b06-352">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="f5b06-352">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f5b06-353">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="f5b06-353">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f5b06-354">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-354">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f5b06-355">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="f5b06-355">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f5b06-356">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-356">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="f5b06-357">**方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-357">**Scenario**</span></span>

<span data-ttu-id="f5b06-358">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-358">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="f5b06-359">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-359">**Solution**</span></span>

<span data-ttu-id="f5b06-360">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-360">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="f5b06-361">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-361">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f5b06-362">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-362">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f5b06-363">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="f5b06-363">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="f5b06-364">通用准则</span><span class="sxs-lookup"><span data-stu-id="f5b06-364">General Guidelines</span></span>

* <span data-ttu-id="f5b06-365">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="f5b06-365">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="f5b06-366">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="f5b06-366">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f5b06-367">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-367">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="f5b06-368">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="f5b06-368">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="f5b06-369">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-369">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f5b06-370"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-370">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f5b06-371">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-371">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="f5b06-372">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-372">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f5b06-373">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="f5b06-373">Default service container replacement</span></span>

<span data-ttu-id="f5b06-374">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="f5b06-374">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f5b06-375">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="f5b06-375">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="f5b06-376">属性注入</span><span class="sxs-lookup"><span data-stu-id="f5b06-376">Property injection</span></span>
* <span data-ttu-id="f5b06-377">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="f5b06-377">Injection based on name</span></span>
* <span data-ttu-id="f5b06-378">子容器</span><span class="sxs-lookup"><span data-stu-id="f5b06-378">Child containers</span></span>
* <span data-ttu-id="f5b06-379">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="f5b06-379">Custom lifetime management</span></span>
* <span data-ttu-id="f5b06-380">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="f5b06-380">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f5b06-381">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="f5b06-381">Convention-based registration</span></span>

<span data-ttu-id="f5b06-382">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="f5b06-382">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f5b06-383">Autofac</span><span class="sxs-lookup"><span data-stu-id="f5b06-383">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f5b06-384">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f5b06-384">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f5b06-385">Grace</span><span class="sxs-lookup"><span data-stu-id="f5b06-385">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f5b06-386">LightInject</span><span class="sxs-lookup"><span data-stu-id="f5b06-386">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f5b06-387">Lamar</span><span class="sxs-lookup"><span data-stu-id="f5b06-387">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f5b06-388">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f5b06-388">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f5b06-389">Unity</span><span class="sxs-lookup"><span data-stu-id="f5b06-389">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="f5b06-390">线程安全</span><span class="sxs-lookup"><span data-stu-id="f5b06-390">Thread safety</span></span>

<span data-ttu-id="f5b06-391">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-391">Create thread-safe singleton services.</span></span> <span data-ttu-id="f5b06-392">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="f5b06-392">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="f5b06-393">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-393">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f5b06-394">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="f5b06-394">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f5b06-395">建议</span><span class="sxs-lookup"><span data-stu-id="f5b06-395">Recommendations</span></span>

* <span data-ttu-id="f5b06-396">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="f5b06-396">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f5b06-397">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-397">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f5b06-398">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="f5b06-398">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f5b06-399">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="f5b06-399">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f5b06-400">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-400">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f5b06-401">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="f5b06-401">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f5b06-402">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="f5b06-402">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="f5b06-403">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-403">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="f5b06-404">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="f5b06-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="f5b06-405">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-405">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="f5b06-406">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="f5b06-406">**Incorrect:**</span></span>

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

  <span data-ttu-id="f5b06-407">**正确**：</span><span class="sxs-lookup"><span data-stu-id="f5b06-407">**Correct**:</span></span>

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

* <span data-ttu-id="f5b06-408">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="f5b06-408">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="f5b06-409">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="f5b06-409">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="f5b06-410">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-410">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="f5b06-411">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="f5b06-411">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f5b06-412">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="f5b06-412">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f5b06-413">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-413">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f5b06-414">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="f5b06-414">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="f5b06-415">DI 中多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="f5b06-415">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="f5b06-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="f5b06-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="f5b06-417">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-417">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="f5b06-418">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-418">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5b06-419">其他资源</span><span class="sxs-lookup"><span data-stu-id="f5b06-419">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f5b06-420">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="f5b06-420">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f5b06-421">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="f5b06-421">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="f5b06-422">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="f5b06-422">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="f5b06-423">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="f5b06-423">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f5b06-424">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-424">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5b06-425">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="f5b06-425">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f5b06-426">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-426">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f5b06-427">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f5b06-427">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f5b06-428">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="f5b06-428">Overview of dependency injection</span></span>

<span data-ttu-id="f5b06-429">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="f5b06-429">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f5b06-430">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="f5b06-430">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="f5b06-431">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-431">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f5b06-432">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="f5b06-432">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="f5b06-433">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-433">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f5b06-434">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="f5b06-434">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f5b06-435">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-435">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f5b06-436">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="f5b06-436">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f5b06-437">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="f5b06-437">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f5b06-438">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="f5b06-438">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f5b06-439">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-439">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f5b06-440">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="f5b06-440">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f5b06-441">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="f5b06-441">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f5b06-442">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-442">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f5b06-443">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-443">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f5b06-444">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="f5b06-444">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f5b06-445">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="f5b06-445">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f5b06-446">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="f5b06-446">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f5b06-447">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="f5b06-447">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="f5b06-448">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="f5b06-448">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="f5b06-449">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-449">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="f5b06-450">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="f5b06-450">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f5b06-451">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-451">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f5b06-452">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-452">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f5b06-453">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="f5b06-453">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f5b06-454">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-454">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f5b06-455">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="f5b06-455">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5b06-456">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-456">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f5b06-457">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="f5b06-457">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="f5b06-458">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-458">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f5b06-459">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-459">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f5b06-460">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-460">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="f5b06-461">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-461">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f5b06-462">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-462">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f5b06-463">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="f5b06-463">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f5b06-464">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="f5b06-464">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f5b06-465">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="f5b06-465">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f5b06-466">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-466">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f5b06-467">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-467">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f5b06-468">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="f5b06-468">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="f5b06-469">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-469">Services injected into Startup</span></span>

<span data-ttu-id="f5b06-470">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="f5b06-470">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f5b06-471">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="f5b06-471">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="f5b06-472">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-472">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f5b06-473">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-473">Framework-provided services</span></span>

<span data-ttu-id="f5b06-474">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="f5b06-474">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f5b06-475">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-475">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f5b06-476">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="f5b06-476">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="f5b06-477">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-477">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="f5b06-478">服务类型</span><span class="sxs-lookup"><span data-stu-id="f5b06-478">Service Type</span></span> | <span data-ttu-id="f5b06-479">生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-479">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f5b06-480">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-480">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="f5b06-481">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-481">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="f5b06-482">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f5b06-483">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f5b06-484">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-484">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f5b06-485">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f5b06-486">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f5b06-487">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f5b06-488">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-488">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f5b06-489">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f5b06-490">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-490">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f5b06-491">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-491">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f5b06-492">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-492">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f5b06-493">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-493">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="f5b06-494">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-494">Register additional services with extension methods</span></span>

<span data-ttu-id="f5b06-495">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-495">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f5b06-496">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-496">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="f5b06-497">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-497">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f5b06-498">服务生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-498">Service lifetimes</span></span>

<span data-ttu-id="f5b06-499">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-499">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f5b06-500">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="f5b06-500">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="f5b06-501">暂时</span><span class="sxs-lookup"><span data-stu-id="f5b06-501">Transient</span></span>

<span data-ttu-id="f5b06-502">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-502">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="f5b06-503">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-503">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="f5b06-504">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-504">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="f5b06-505">范围内</span><span class="sxs-lookup"><span data-stu-id="f5b06-505">Scoped</span></span>

<span data-ttu-id="f5b06-506">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="f5b06-506">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="f5b06-507">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-507">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="f5b06-508">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f5b06-509">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="f5b06-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f5b06-510">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f5b06-511">单例</span><span class="sxs-lookup"><span data-stu-id="f5b06-511">Singleton</span></span>

<span data-ttu-id="f5b06-512">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f5b06-513">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-513">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f5b06-514">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f5b06-515">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="f5b06-516">在处理请求的应用中，在应用关闭，释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-516">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="f5b06-517">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="f5b06-517">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f5b06-518">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="f5b06-518">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f5b06-519">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="f5b06-519">Service registration methods</span></span>

<span data-ttu-id="f5b06-520">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="f5b06-520">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="f5b06-521">方法</span><span class="sxs-lookup"><span data-stu-id="f5b06-521">Method</span></span> | <span data-ttu-id="f5b06-522">自动</span><span class="sxs-lookup"><span data-stu-id="f5b06-522">Automatic</span></span><br><span data-ttu-id="f5b06-523">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="f5b06-523">object</span></span><br><span data-ttu-id="f5b06-524">处置</span><span class="sxs-lookup"><span data-stu-id="f5b06-524">disposal</span></span> | <span data-ttu-id="f5b06-525">多种</span><span class="sxs-lookup"><span data-stu-id="f5b06-525">Multiple</span></span><br><span data-ttu-id="f5b06-526">实现</span><span class="sxs-lookup"><span data-stu-id="f5b06-526">implementations</span></span> | <span data-ttu-id="f5b06-527">传递参数</span><span class="sxs-lookup"><span data-stu-id="f5b06-527">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f5b06-528">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-528">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="f5b06-529">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-529">Yes</span></span> | <span data-ttu-id="f5b06-530">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-530">Yes</span></span> | <span data-ttu-id="f5b06-531">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-531">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f5b06-532">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-532">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="f5b06-533">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-533">Yes</span></span> | <span data-ttu-id="f5b06-534">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-534">Yes</span></span> | <span data-ttu-id="f5b06-535">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-535">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f5b06-536">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-536">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="f5b06-537">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-537">Yes</span></span> | <span data-ttu-id="f5b06-538">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-538">No</span></span> | <span data-ttu-id="f5b06-539">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-539">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f5b06-540">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-540">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="f5b06-541">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-541">No</span></span> | <span data-ttu-id="f5b06-542">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-542">Yes</span></span> | <span data-ttu-id="f5b06-543">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-543">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f5b06-544">示例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-544">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="f5b06-545">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-545">No</span></span> | <span data-ttu-id="f5b06-546">否</span><span class="sxs-lookup"><span data-stu-id="f5b06-546">No</span></span> | <span data-ttu-id="f5b06-547">是</span><span class="sxs-lookup"><span data-stu-id="f5b06-547">Yes</span></span> |

<span data-ttu-id="f5b06-548">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="f5b06-548">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f5b06-549">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-549">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f5b06-550">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-550">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f5b06-551">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-551">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="f5b06-552">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="f5b06-552">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f5b06-553">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="f5b06-553">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="f5b06-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f5b06-555">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="f5b06-555">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f5b06-556">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-556">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f5b06-557">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="f5b06-557">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="f5b06-558">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-558">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="f5b06-559">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-559">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="f5b06-560">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="f5b06-560">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f5b06-561">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="f5b06-561">Constructor injection behavior</span></span>

<span data-ttu-id="f5b06-562">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="f5b06-562">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f5b06-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="f5b06-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f5b06-564">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="f5b06-564">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f5b06-565">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="f5b06-565">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f5b06-566">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="f5b06-566">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="f5b06-567">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="f5b06-567">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f5b06-568">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="f5b06-568">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f5b06-569">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="f5b06-569">Entity Framework contexts</span></span>

<span data-ttu-id="f5b06-570">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="f5b06-570">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f5b06-571">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="f5b06-571">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="f5b06-572">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="f5b06-572">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f5b06-573">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="f5b06-573">Lifetime and registration options</span></span>

<span data-ttu-id="f5b06-574">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="f5b06-574">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f5b06-575">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-575">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="f5b06-576">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="f5b06-576">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f5b06-577">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="f5b06-577">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="f5b06-578">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="f5b06-578">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f5b06-579">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-579">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f5b06-580">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="f5b06-580">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f5b06-581">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-581">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f5b06-582">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-582">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f5b06-583">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="f5b06-583">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="f5b06-584">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="f5b06-584">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f5b06-585">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="f5b06-585">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="f5b06-586">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="f5b06-586">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="f5b06-587">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-587">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f5b06-588">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-588">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="f5b06-589">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-589">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f5b06-590">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="f5b06-590">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f5b06-591">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="f5b06-591">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="f5b06-592">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="f5b06-592">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f5b06-593">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="f5b06-593">**First request:**</span></span>

<span data-ttu-id="f5b06-594">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-594">Controller operations:</span></span>

<span data-ttu-id="f5b06-595">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f5b06-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f5b06-596">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f5b06-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f5b06-597">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-598">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-598">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-599">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-599">`OperationService` operations:</span></span>

<span data-ttu-id="f5b06-600">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f5b06-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f5b06-601">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f5b06-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f5b06-602">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-603">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-604">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="f5b06-604">**Second request:**</span></span>

<span data-ttu-id="f5b06-605">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-605">Controller operations:</span></span>

<span data-ttu-id="f5b06-606">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f5b06-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f5b06-607">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f5b06-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f5b06-608">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-609">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-609">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-610">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="f5b06-610">`OperationService` operations:</span></span>

<span data-ttu-id="f5b06-611">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f5b06-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f5b06-612">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f5b06-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f5b06-613">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f5b06-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f5b06-614">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f5b06-614">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f5b06-615">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="f5b06-615">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f5b06-616">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="f5b06-616">*Transient* objects are always different.</span></span> <span data-ttu-id="f5b06-617">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-617">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="f5b06-618">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-618">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="f5b06-619">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-619">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="f5b06-620">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-620">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f5b06-621">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-621">Call services from main</span></span>

<span data-ttu-id="f5b06-622">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-622">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f5b06-623">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-623">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f5b06-624">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="f5b06-624">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="f5b06-625">作用域验证</span><span class="sxs-lookup"><span data-stu-id="f5b06-625">Scope validation</span></span>

<span data-ttu-id="f5b06-626">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="f5b06-626">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f5b06-627">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-627">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f5b06-628">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-628">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f5b06-629">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="f5b06-629">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="f5b06-630">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="f5b06-630">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f5b06-631">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="f5b06-631">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f5b06-632">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="f5b06-632">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f5b06-633">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="f5b06-633">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f5b06-634">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-634">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="f5b06-635">请求服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-635">Request Services</span></span>

<span data-ttu-id="f5b06-636">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="f5b06-636">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="f5b06-637">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-637">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f5b06-638">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="f5b06-638">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f5b06-639">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="f5b06-639">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f5b06-640">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-640">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="f5b06-641">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="f5b06-641">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="f5b06-642">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="f5b06-642">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f5b06-643">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-643">Design services for dependency injection</span></span>

<span data-ttu-id="f5b06-644">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="f5b06-644">Best practices are to:</span></span>

* <span data-ttu-id="f5b06-645">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-645">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f5b06-646">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="f5b06-646">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f5b06-647">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="f5b06-647">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="f5b06-648">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-648">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f5b06-649">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="f5b06-649">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f5b06-650">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="f5b06-650">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f5b06-651">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-651">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f5b06-652">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="f5b06-652">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f5b06-653">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="f5b06-653">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f5b06-654">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="f5b06-654">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f5b06-655">服务处理</span><span class="sxs-lookup"><span data-stu-id="f5b06-655">Disposal of services</span></span>

<span data-ttu-id="f5b06-656">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-656">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f5b06-657">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-657">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="f5b06-658">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="f5b06-658">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="f5b06-659">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f5b06-659">In the following example:</span></span>

* <span data-ttu-id="f5b06-660">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-660">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="f5b06-661">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-661">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="f5b06-662">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-662">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="f5b06-663">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="f5b06-663">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="f5b06-664">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="f5b06-664">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="f5b06-665">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-665">Transient, limited lifetime</span></span>

<span data-ttu-id="f5b06-666">**方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-666">**Scenario**</span></span>

<span data-ttu-id="f5b06-667">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="f5b06-667">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="f5b06-668">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-668">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="f5b06-669">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-669">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="f5b06-670">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-670">**Solution**</span></span>

<span data-ttu-id="f5b06-671">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-671">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="f5b06-672">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="f5b06-672">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="f5b06-673">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="f5b06-673">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="f5b06-674">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-674">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="f5b06-675">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="f5b06-675">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="f5b06-676">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="f5b06-676">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="f5b06-677">**方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-677">**Scenario**</span></span>

<span data-ttu-id="f5b06-678">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-678">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="f5b06-679">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="f5b06-679">**Solution**</span></span>

<span data-ttu-id="f5b06-680">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-680">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="f5b06-681">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-681">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="f5b06-682">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-682">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="f5b06-683">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="f5b06-683">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="f5b06-684">通用准则</span><span class="sxs-lookup"><span data-stu-id="f5b06-684">General Guidelines</span></span>

* <span data-ttu-id="f5b06-685">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="f5b06-685">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="f5b06-686">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="f5b06-686">Use the factory pattern instead.</span></span>
* <span data-ttu-id="f5b06-687">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="f5b06-687">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="f5b06-688">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="f5b06-688">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="f5b06-689">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-689">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="f5b06-690"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="f5b06-690">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="f5b06-691">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="f5b06-691">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="f5b06-692">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="f5b06-692">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="f5b06-693">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="f5b06-693">Default service container replacement</span></span>

<span data-ttu-id="f5b06-694">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="f5b06-694">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f5b06-695">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="f5b06-695">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="f5b06-696">属性注入</span><span class="sxs-lookup"><span data-stu-id="f5b06-696">Property injection</span></span>
* <span data-ttu-id="f5b06-697">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="f5b06-697">Injection based on name</span></span>
* <span data-ttu-id="f5b06-698">子容器</span><span class="sxs-lookup"><span data-stu-id="f5b06-698">Child containers</span></span>
* <span data-ttu-id="f5b06-699">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="f5b06-699">Custom lifetime management</span></span>
* <span data-ttu-id="f5b06-700">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="f5b06-700">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="f5b06-701">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="f5b06-701">Convention-based registration</span></span>

<span data-ttu-id="f5b06-702">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="f5b06-702">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f5b06-703">Autofac</span><span class="sxs-lookup"><span data-stu-id="f5b06-703">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f5b06-704">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f5b06-704">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f5b06-705">Grace</span><span class="sxs-lookup"><span data-stu-id="f5b06-705">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f5b06-706">LightInject</span><span class="sxs-lookup"><span data-stu-id="f5b06-706">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f5b06-707">Lamar</span><span class="sxs-lookup"><span data-stu-id="f5b06-707">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f5b06-708">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f5b06-708">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f5b06-709">Unity</span><span class="sxs-lookup"><span data-stu-id="f5b06-709">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="f5b06-710">线程安全</span><span class="sxs-lookup"><span data-stu-id="f5b06-710">Thread safety</span></span>

<span data-ttu-id="f5b06-711">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="f5b06-711">Create thread-safe singleton services.</span></span> <span data-ttu-id="f5b06-712">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="f5b06-712">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="f5b06-713">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="f5b06-713">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f5b06-714">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="f5b06-714">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f5b06-715">建议</span><span class="sxs-lookup"><span data-stu-id="f5b06-715">Recommendations</span></span>

* <span data-ttu-id="f5b06-716">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="f5b06-716">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f5b06-717">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-717">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f5b06-718">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="f5b06-718">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f5b06-719">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="f5b06-719">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f5b06-720">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="f5b06-720">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f5b06-721">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="f5b06-721">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f5b06-722">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="f5b06-722">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="f5b06-723">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-723">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="f5b06-724">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="f5b06-724">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="f5b06-725">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="f5b06-725">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="f5b06-726">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="f5b06-726">**Incorrect:**</span></span>

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

    <span data-ttu-id="f5b06-727">**正确**：</span><span class="sxs-lookup"><span data-stu-id="f5b06-727">**Correct**:</span></span>

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

  * <span data-ttu-id="f5b06-728">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="f5b06-728">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="f5b06-729">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="f5b06-729">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="f5b06-730">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="f5b06-730">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f5b06-731">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="f5b06-731">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f5b06-732">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="f5b06-732">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f5b06-733">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="f5b06-733">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5b06-734">其他资源</span><span class="sxs-lookup"><span data-stu-id="f5b06-734">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f5b06-735">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="f5b06-735">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="f5b06-736">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="f5b06-736">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="f5b06-737">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="f5b06-737">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="f5b06-738">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="f5b06-738">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f5b06-739">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="f5b06-739">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
