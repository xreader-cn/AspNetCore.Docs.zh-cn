---
title: 在 ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ddb583f69758055500ff63960f469c1cea44c77e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102589"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0d7b0-103">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="0d7b0-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="0d7b0-104">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="0d7b0-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d7b0-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0d7b0-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0d7b0-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0d7b0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0d7b0-108">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="0d7b0-108">Overview of dependency injection</span></span>

<span data-ttu-id="0d7b0-109">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0d7b0-110">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0d7b0-111">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0d7b0-112">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0d7b0-113">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0d7b0-114">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0d7b0-115">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0d7b0-116">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0d7b0-117">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0d7b0-118">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0d7b0-119">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0d7b0-120">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0d7b0-121">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0d7b0-122">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0d7b0-123">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0d7b0-124">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0d7b0-125">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0d7b0-126">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0d7b0-127">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0d7b0-128">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0d7b0-129">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0d7b0-130">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0d7b0-131">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0d7b0-132">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0d7b0-133">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0d7b0-134">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0d7b0-135">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0d7b0-136">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0d7b0-137">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0d7b0-138">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0d7b0-139">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0d7b0-140">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0d7b0-141">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0d7b0-142">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0d7b0-143">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0d7b0-144">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0d7b0-145">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0d7b0-146">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0d7b0-147">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0d7b0-148">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0d7b0-149">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-149">Services injected into Startup</span></span>

<span data-ttu-id="0d7b0-150">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0d7b0-151">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0d7b0-152">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0d7b0-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-153">Framework-provided services</span></span>

<span data-ttu-id="0d7b0-154">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0d7b0-155">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0d7b0-156">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0d7b0-157">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0d7b0-158">服务类型</span><span class="sxs-lookup"><span data-stu-id="0d7b0-158">Service Type</span></span> | <span data-ttu-id="0d7b0-159">生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0d7b0-160">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="0d7b0-161">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="0d7b0-162">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0d7b0-163">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0d7b0-164">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0d7b0-165">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0d7b0-166">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0d7b0-167">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0d7b0-168">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0d7b0-169">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0d7b0-170">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0d7b0-171">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0d7b0-172">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0d7b0-173">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0d7b0-174">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-174">Register additional services with extension methods</span></span>

<span data-ttu-id="0d7b0-175">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0d7b0-176">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0d7b0-177">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0d7b0-178">服务生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-178">Service lifetimes</span></span>

<span data-ttu-id="0d7b0-179">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0d7b0-180">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0d7b0-181">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-181">Transient</span></span>

<span data-ttu-id="0d7b0-182">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0d7b0-183">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0d7b0-184">范围内</span><span class="sxs-lookup"><span data-stu-id="0d7b0-184">Scoped</span></span>

<span data-ttu-id="0d7b0-185">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0d7b0-186">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0d7b0-187">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0d7b0-188">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0d7b0-189">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-189">Singleton</span></span>

<span data-ttu-id="0d7b0-190">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0d7b0-191">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0d7b0-192">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0d7b0-193">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0d7b0-194">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0d7b0-195">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0d7b0-196">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="0d7b0-196">Service registration methods</span></span>

<span data-ttu-id="0d7b0-197">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0d7b0-198">方法</span><span class="sxs-lookup"><span data-stu-id="0d7b0-198">Method</span></span> | <span data-ttu-id="0d7b0-199">自动</span><span class="sxs-lookup"><span data-stu-id="0d7b0-199">Automatic</span></span><br><span data-ttu-id="0d7b0-200">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="0d7b0-200">object</span></span><br><span data-ttu-id="0d7b0-201">处置</span><span class="sxs-lookup"><span data-stu-id="0d7b0-201">disposal</span></span> | <span data-ttu-id="0d7b0-202">多种</span><span class="sxs-lookup"><span data-stu-id="0d7b0-202">Multiple</span></span><br><span data-ttu-id="0d7b0-203">实现</span><span class="sxs-lookup"><span data-stu-id="0d7b0-203">implementations</span></span> | <span data-ttu-id="0d7b0-204">传递参数</span><span class="sxs-lookup"><span data-stu-id="0d7b0-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0d7b0-205">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0d7b0-206">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-206">Yes</span></span> | <span data-ttu-id="0d7b0-207">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-207">Yes</span></span> | <span data-ttu-id="0d7b0-208">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0d7b0-209">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0d7b0-210">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-210">Yes</span></span> | <span data-ttu-id="0d7b0-211">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-211">Yes</span></span> | <span data-ttu-id="0d7b0-212">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0d7b0-213">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0d7b0-214">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-214">Yes</span></span> | <span data-ttu-id="0d7b0-215">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-215">No</span></span> | <span data-ttu-id="0d7b0-216">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0d7b0-217">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0d7b0-218">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-218">No</span></span> | <span data-ttu-id="0d7b0-219">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-219">Yes</span></span> | <span data-ttu-id="0d7b0-220">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0d7b0-221">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0d7b0-222">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-222">No</span></span> | <span data-ttu-id="0d7b0-223">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-223">No</span></span> | <span data-ttu-id="0d7b0-224">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-224">Yes</span></span> |

<span data-ttu-id="0d7b0-225">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0d7b0-226">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0d7b0-227">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0d7b0-228">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0d7b0-229">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0d7b0-230">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0d7b0-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0d7b0-232">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0d7b0-233">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0d7b0-234">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0d7b0-235">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0d7b0-236">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0d7b0-237">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0d7b0-238">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="0d7b0-238">Constructor injection behavior</span></span>

<span data-ttu-id="0d7b0-239">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0d7b0-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0d7b0-241">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0d7b0-242">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0d7b0-243">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0d7b0-244">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0d7b0-245">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0d7b0-246">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="0d7b0-246">Entity Framework contexts</span></span>

<span data-ttu-id="0d7b0-247">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0d7b0-248">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0d7b0-249">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0d7b0-250">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="0d7b0-250">Lifetime and registration options</span></span>

<span data-ttu-id="0d7b0-251">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0d7b0-252">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0d7b0-253">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0d7b0-254">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0d7b0-255">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0d7b0-256">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0d7b0-257">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0d7b0-258">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0d7b0-259">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0d7b0-260">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0d7b0-261">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0d7b0-262">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0d7b0-263">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0d7b0-264">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0d7b0-265">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0d7b0-266">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0d7b0-267">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0d7b0-268">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0d7b0-269">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0d7b0-270">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-270">**First request:**</span></span>

<span data-ttu-id="0d7b0-271">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-271">Controller operations:</span></span>

<span data-ttu-id="0d7b0-272">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0d7b0-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0d7b0-273">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0d7b0-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0d7b0-274">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-275">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-276">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-276">`OperationService` operations:</span></span>

<span data-ttu-id="0d7b0-277">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0d7b0-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0d7b0-278">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0d7b0-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0d7b0-279">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-280">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-281">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-281">**Second request:**</span></span>

<span data-ttu-id="0d7b0-282">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-282">Controller operations:</span></span>

<span data-ttu-id="0d7b0-283">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0d7b0-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0d7b0-284">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0d7b0-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0d7b0-285">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-286">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-287">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-287">`OperationService` operations:</span></span>

<span data-ttu-id="0d7b0-288">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0d7b0-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0d7b0-289">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0d7b0-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0d7b0-290">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-291">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-292">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0d7b0-293">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-293">*Transient* objects are always different.</span></span> <span data-ttu-id="0d7b0-294">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0d7b0-295">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0d7b0-296">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0d7b0-297">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0d7b0-298">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-298">Call services from main</span></span>

<span data-ttu-id="0d7b0-299">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0d7b0-300">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0d7b0-301">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0d7b0-302">作用域验证</span><span class="sxs-lookup"><span data-stu-id="0d7b0-302">Scope validation</span></span>

<span data-ttu-id="0d7b0-303">如果应用正在开发环境中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0d7b0-304">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0d7b0-305">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0d7b0-306">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0d7b0-307">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0d7b0-308">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0d7b0-309">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0d7b0-310">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0d7b0-311">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0d7b0-312">请求服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-312">Request Services</span></span>

<span data-ttu-id="0d7b0-313">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0d7b0-314">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0d7b0-315">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0d7b0-316">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0d7b0-317">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="0d7b0-318">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0d7b0-319">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0d7b0-320">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-320">Design services for dependency injection</span></span>

<span data-ttu-id="0d7b0-321">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-321">Best practices are to:</span></span>

* <span data-ttu-id="0d7b0-322">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0d7b0-323">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0d7b0-324">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0d7b0-325">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0d7b0-326">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0d7b0-327">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0d7b0-328">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0d7b0-329">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0d7b0-330">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0d7b0-331">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0d7b0-332">服务处理</span><span class="sxs-lookup"><span data-stu-id="0d7b0-332">Disposal of services</span></span>

<span data-ttu-id="0d7b0-333">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0d7b0-334">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0d7b0-335">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0d7b0-336">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-336">In the following example:</span></span>

* <span data-ttu-id="0d7b0-337">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0d7b0-338">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0d7b0-339">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0d7b0-340">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0d7b0-341">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="0d7b0-341">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0d7b0-342">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-342">Transient, limited lifetime</span></span>

<span data-ttu-id="0d7b0-343">**方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-343">**Scenario**</span></span>

<span data-ttu-id="0d7b0-344">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-344">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0d7b0-345">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-345">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="0d7b0-346">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-346">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0d7b0-347">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-347">**Solution**</span></span>

<span data-ttu-id="0d7b0-348">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-348">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0d7b0-349">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-349">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0d7b0-350">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-350">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0d7b0-351">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-351">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0d7b0-352">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-352">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0d7b0-353">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-353">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0d7b0-354">**方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-354">**Scenario**</span></span>

<span data-ttu-id="0d7b0-355">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-355">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0d7b0-356">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-356">**Solution**</span></span>

<span data-ttu-id="0d7b0-357">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-357">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0d7b0-358">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-358">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0d7b0-359">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-359">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0d7b0-360">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-360">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="0d7b0-361">通用准则</span><span class="sxs-lookup"><span data-stu-id="0d7b0-361">General Guidelines</span></span>

* <span data-ttu-id="0d7b0-362">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-362">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0d7b0-363">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-363">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0d7b0-364">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-364">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0d7b0-365">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-365">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0d7b0-366">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-366">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0d7b0-367"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-367">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0d7b0-368">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-368">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0d7b0-369">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-369">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0d7b0-370">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="0d7b0-370">Default service container replacement</span></span>

<span data-ttu-id="0d7b0-371">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-371">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0d7b0-372">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-372">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0d7b0-373">属性注入</span><span class="sxs-lookup"><span data-stu-id="0d7b0-373">Property injection</span></span>
* <span data-ttu-id="0d7b0-374">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="0d7b0-374">Injection based on name</span></span>
* <span data-ttu-id="0d7b0-375">子容器</span><span class="sxs-lookup"><span data-stu-id="0d7b0-375">Child containers</span></span>
* <span data-ttu-id="0d7b0-376">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="0d7b0-376">Custom lifetime management</span></span>
* <span data-ttu-id="0d7b0-377">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="0d7b0-377">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0d7b0-378">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="0d7b0-378">Convention-based registration</span></span>

<span data-ttu-id="0d7b0-379">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-379">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0d7b0-380">Autofac</span><span class="sxs-lookup"><span data-stu-id="0d7b0-380">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0d7b0-381">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0d7b0-381">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0d7b0-382">Grace</span><span class="sxs-lookup"><span data-stu-id="0d7b0-382">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0d7b0-383">LightInject</span><span class="sxs-lookup"><span data-stu-id="0d7b0-383">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0d7b0-384">Lamar</span><span class="sxs-lookup"><span data-stu-id="0d7b0-384">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0d7b0-385">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0d7b0-385">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0d7b0-386">Unity</span><span class="sxs-lookup"><span data-stu-id="0d7b0-386">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0d7b0-387">线程安全</span><span class="sxs-lookup"><span data-stu-id="0d7b0-387">Thread safety</span></span>

<span data-ttu-id="0d7b0-388">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-388">Create thread-safe singleton services.</span></span> <span data-ttu-id="0d7b0-389">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-389">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0d7b0-390">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-390">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0d7b0-391">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-391">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0d7b0-392">建议</span><span class="sxs-lookup"><span data-stu-id="0d7b0-392">Recommendations</span></span>

* <span data-ttu-id="0d7b0-393">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-393">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0d7b0-394">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-394">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0d7b0-395">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-395">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0d7b0-396">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-396">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0d7b0-397">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-397">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0d7b0-398">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-398">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0d7b0-399">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-399">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0d7b0-400">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-400">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0d7b0-401">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-401">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0d7b0-402">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-402">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0d7b0-403">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-403">**Incorrect:**</span></span>

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

  <span data-ttu-id="0d7b0-404">**正确**：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-404">**Correct**:</span></span>

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

* <span data-ttu-id="0d7b0-405">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-405">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0d7b0-406">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-406">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="0d7b0-407">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-407">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0d7b0-408">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-408">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0d7b0-409">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-409">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0d7b0-410">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-410">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0d7b0-411">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-411">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="0d7b0-412">DI 中多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="0d7b0-412">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="0d7b0-413">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-413">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="0d7b0-414">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-414">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="0d7b0-415">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-415">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d7b0-416">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d7b0-416">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0d7b0-417">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="0d7b0-417">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0d7b0-418">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="0d7b0-418">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="0d7b0-419">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="0d7b0-419">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="0d7b0-420">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0d7b0-420">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0d7b0-421">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-421">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0d7b0-422">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-422">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0d7b0-423">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-423">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0d7b0-424">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0d7b0-424">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0d7b0-425">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="0d7b0-425">Overview of dependency injection</span></span>

<span data-ttu-id="0d7b0-426">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-426">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0d7b0-427">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-427">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0d7b0-428">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-428">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0d7b0-429">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-429">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0d7b0-430">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-430">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0d7b0-431">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-431">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0d7b0-432">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-432">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0d7b0-433">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-433">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0d7b0-434">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-434">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0d7b0-435">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-435">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0d7b0-436">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-436">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0d7b0-437">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-437">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0d7b0-438">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-438">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0d7b0-439">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-439">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0d7b0-440">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-440">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0d7b0-441">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-441">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0d7b0-442">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-442">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0d7b0-443">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-443">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0d7b0-444">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-444">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0d7b0-445">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-445">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0d7b0-446">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-446">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0d7b0-447">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-447">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0d7b0-448">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-448">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0d7b0-449">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-449">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0d7b0-450">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-450">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0d7b0-451">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-451">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0d7b0-452">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-452">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0d7b0-453">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-453">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0d7b0-454">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-454">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0d7b0-455">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-455">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0d7b0-456">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-456">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0d7b0-457">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-457">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0d7b0-458">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-458">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0d7b0-459">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-459">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0d7b0-460">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-460">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0d7b0-461">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-461">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0d7b0-462">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-462">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0d7b0-463">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-463">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0d7b0-464">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-464">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0d7b0-465">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-465">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0d7b0-466">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-466">Services injected into Startup</span></span>

<span data-ttu-id="0d7b0-467">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-467">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0d7b0-468">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-468">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0d7b0-469">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-469">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0d7b0-470">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-470">Framework-provided services</span></span>

<span data-ttu-id="0d7b0-471">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-471">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0d7b0-472">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-472">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0d7b0-473">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-473">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0d7b0-474">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-474">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0d7b0-475">服务类型</span><span class="sxs-lookup"><span data-stu-id="0d7b0-475">Service Type</span></span> | <span data-ttu-id="0d7b0-476">生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-476">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0d7b0-477">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-477">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0d7b0-478">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-478">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0d7b0-479">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-479">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0d7b0-480">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-480">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0d7b0-481">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-481">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0d7b0-482">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0d7b0-483">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-483">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0d7b0-484">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0d7b0-485">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0d7b0-486">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-486">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0d7b0-487">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-487">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0d7b0-488">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-488">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0d7b0-489">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0d7b0-490">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0d7b0-491">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-491">Register additional services with extension methods</span></span>

<span data-ttu-id="0d7b0-492">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-492">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0d7b0-493">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-493">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0d7b0-494">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-494">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0d7b0-495">服务生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-495">Service lifetimes</span></span>

<span data-ttu-id="0d7b0-496">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-496">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0d7b0-497">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-497">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0d7b0-498">暂时</span><span class="sxs-lookup"><span data-stu-id="0d7b0-498">Transient</span></span>

<span data-ttu-id="0d7b0-499">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-499">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0d7b0-500">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-500">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0d7b0-501">范围内</span><span class="sxs-lookup"><span data-stu-id="0d7b0-501">Scoped</span></span>

<span data-ttu-id="0d7b0-502">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-502">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0d7b0-503">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-503">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0d7b0-504">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-504">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0d7b0-505">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-505">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0d7b0-506">单例</span><span class="sxs-lookup"><span data-stu-id="0d7b0-506">Singleton</span></span>

<span data-ttu-id="0d7b0-507">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-507">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0d7b0-508">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-508">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0d7b0-509">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-509">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0d7b0-510">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-510">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0d7b0-511">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-511">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0d7b0-512">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-512">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0d7b0-513">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="0d7b0-513">Service registration methods</span></span>

<span data-ttu-id="0d7b0-514">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-514">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0d7b0-515">方法</span><span class="sxs-lookup"><span data-stu-id="0d7b0-515">Method</span></span> | <span data-ttu-id="0d7b0-516">自动</span><span class="sxs-lookup"><span data-stu-id="0d7b0-516">Automatic</span></span><br><span data-ttu-id="0d7b0-517">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="0d7b0-517">object</span></span><br><span data-ttu-id="0d7b0-518">处置</span><span class="sxs-lookup"><span data-stu-id="0d7b0-518">disposal</span></span> | <span data-ttu-id="0d7b0-519">多种</span><span class="sxs-lookup"><span data-stu-id="0d7b0-519">Multiple</span></span><br><span data-ttu-id="0d7b0-520">实现</span><span class="sxs-lookup"><span data-stu-id="0d7b0-520">implementations</span></span> | <span data-ttu-id="0d7b0-521">传递参数</span><span class="sxs-lookup"><span data-stu-id="0d7b0-521">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0d7b0-522">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-522">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0d7b0-523">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-523">Yes</span></span> | <span data-ttu-id="0d7b0-524">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-524">Yes</span></span> | <span data-ttu-id="0d7b0-525">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-525">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0d7b0-526">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-526">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0d7b0-527">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-527">Yes</span></span> | <span data-ttu-id="0d7b0-528">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-528">Yes</span></span> | <span data-ttu-id="0d7b0-529">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0d7b0-530">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-530">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0d7b0-531">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-531">Yes</span></span> | <span data-ttu-id="0d7b0-532">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-532">No</span></span> | <span data-ttu-id="0d7b0-533">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-533">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0d7b0-534">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-534">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0d7b0-535">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-535">No</span></span> | <span data-ttu-id="0d7b0-536">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-536">Yes</span></span> | <span data-ttu-id="0d7b0-537">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-537">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0d7b0-538">示例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-538">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0d7b0-539">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-539">No</span></span> | <span data-ttu-id="0d7b0-540">否</span><span class="sxs-lookup"><span data-stu-id="0d7b0-540">No</span></span> | <span data-ttu-id="0d7b0-541">是</span><span class="sxs-lookup"><span data-stu-id="0d7b0-541">Yes</span></span> |

<span data-ttu-id="0d7b0-542">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-542">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0d7b0-543">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-543">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0d7b0-544">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-544">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0d7b0-545">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-545">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0d7b0-546">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-546">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0d7b0-547">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-547">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0d7b0-548">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-548">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0d7b0-549">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-549">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0d7b0-550">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-550">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0d7b0-551">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-551">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0d7b0-552">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-552">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0d7b0-553">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-553">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0d7b0-554">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-554">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0d7b0-555">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="0d7b0-555">Constructor injection behavior</span></span>

<span data-ttu-id="0d7b0-556">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-556">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0d7b0-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-557"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0d7b0-558">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-558">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0d7b0-559">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-559">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0d7b0-560">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-560">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0d7b0-561">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-561">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0d7b0-562">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-562">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0d7b0-563">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="0d7b0-563">Entity Framework contexts</span></span>

<span data-ttu-id="0d7b0-564">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-564">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0d7b0-565">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-565">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0d7b0-566">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-566">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0d7b0-567">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="0d7b0-567">Lifetime and registration options</span></span>

<span data-ttu-id="0d7b0-568">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-568">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0d7b0-569">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-569">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0d7b0-570">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-570">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0d7b0-571">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-571">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0d7b0-572">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-572">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0d7b0-573">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-573">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0d7b0-574">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-574">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0d7b0-575">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-575">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0d7b0-576">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-576">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0d7b0-577">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-577">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0d7b0-578">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-578">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0d7b0-579">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-579">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0d7b0-580">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-580">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0d7b0-581">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-581">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0d7b0-582">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-582">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0d7b0-583">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-583">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0d7b0-584">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-584">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0d7b0-585">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-585">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0d7b0-586">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-586">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0d7b0-587">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-587">**First request:**</span></span>

<span data-ttu-id="0d7b0-588">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-588">Controller operations:</span></span>

<span data-ttu-id="0d7b0-589">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0d7b0-589">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0d7b0-590">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0d7b0-590">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0d7b0-591">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-591">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-592">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-592">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-593">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-593">`OperationService` operations:</span></span>

<span data-ttu-id="0d7b0-594">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0d7b0-594">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0d7b0-595">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0d7b0-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0d7b0-596">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-597">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-598">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-598">**Second request:**</span></span>

<span data-ttu-id="0d7b0-599">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-599">Controller operations:</span></span>

<span data-ttu-id="0d7b0-600">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0d7b0-600">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0d7b0-601">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0d7b0-601">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0d7b0-602">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-603">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-604">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-604">`OperationService` operations:</span></span>

<span data-ttu-id="0d7b0-605">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0d7b0-605">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0d7b0-606">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0d7b0-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0d7b0-607">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d7b0-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d7b0-608">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d7b0-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d7b0-609">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-609">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0d7b0-610">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-610">*Transient* objects are always different.</span></span> <span data-ttu-id="0d7b0-611">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-611">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0d7b0-612">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-612">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0d7b0-613">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-613">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0d7b0-614">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-614">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0d7b0-615">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-615">Call services from main</span></span>

<span data-ttu-id="0d7b0-616">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-616">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0d7b0-617">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-617">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0d7b0-618">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-618">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0d7b0-619">作用域验证</span><span class="sxs-lookup"><span data-stu-id="0d7b0-619">Scope validation</span></span>

<span data-ttu-id="0d7b0-620">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-620">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0d7b0-621">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-621">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0d7b0-622">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-622">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0d7b0-623">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-623">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0d7b0-624">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-624">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0d7b0-625">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-625">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0d7b0-626">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-626">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0d7b0-627">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-627">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0d7b0-628">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-628">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0d7b0-629">请求服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-629">Request Services</span></span>

<span data-ttu-id="0d7b0-630">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-630">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0d7b0-631">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-631">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0d7b0-632">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-632">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0d7b0-633">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-633">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0d7b0-634">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-634">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0d7b0-635">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-635">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0d7b0-636">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-636">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0d7b0-637">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-637">Design services for dependency injection</span></span>

<span data-ttu-id="0d7b0-638">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-638">Best practices are to:</span></span>

* <span data-ttu-id="0d7b0-639">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-639">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0d7b0-640">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-640">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0d7b0-641">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-641">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0d7b0-642">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-642">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0d7b0-643">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-643">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0d7b0-644">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-644">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0d7b0-645">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-645">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0d7b0-646">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-646">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0d7b0-647">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-647">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0d7b0-648">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-648">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0d7b0-649">服务处理</span><span class="sxs-lookup"><span data-stu-id="0d7b0-649">Disposal of services</span></span>

<span data-ttu-id="0d7b0-650">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-650">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0d7b0-651">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-651">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0d7b0-652">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-652">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0d7b0-653">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-653">In the following example:</span></span>

* <span data-ttu-id="0d7b0-654">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-654">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0d7b0-655">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-655">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0d7b0-656">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-656">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0d7b0-657">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-657">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0d7b0-658">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="0d7b0-658">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0d7b0-659">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-659">Transient, limited lifetime</span></span>

<span data-ttu-id="0d7b0-660">**方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-660">**Scenario**</span></span>

<span data-ttu-id="0d7b0-661">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-661">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0d7b0-662">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-662">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="0d7b0-663">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-663">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0d7b0-664">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-664">**Solution**</span></span>

<span data-ttu-id="0d7b0-665">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-665">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0d7b0-666">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-666">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0d7b0-667">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-667">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0d7b0-668">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-668">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0d7b0-669">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-669">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0d7b0-670">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="0d7b0-670">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0d7b0-671">**方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-671">**Scenario**</span></span>

<span data-ttu-id="0d7b0-672">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-672">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0d7b0-673">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-673">**Solution**</span></span>

<span data-ttu-id="0d7b0-674">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-674">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0d7b0-675">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-675">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0d7b0-676">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-676">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0d7b0-677">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-677">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="0d7b0-678">通用准则</span><span class="sxs-lookup"><span data-stu-id="0d7b0-678">General Guidelines</span></span>

* <span data-ttu-id="0d7b0-679">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-679">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0d7b0-680">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-680">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0d7b0-681">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-681">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0d7b0-682">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-682">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0d7b0-683">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-683">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0d7b0-684"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-684">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0d7b0-685">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-685">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0d7b0-686">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-686">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0d7b0-687">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="0d7b0-687">Default service container replacement</span></span>

<span data-ttu-id="0d7b0-688">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-688">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0d7b0-689">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-689">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0d7b0-690">属性注入</span><span class="sxs-lookup"><span data-stu-id="0d7b0-690">Property injection</span></span>
* <span data-ttu-id="0d7b0-691">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="0d7b0-691">Injection based on name</span></span>
* <span data-ttu-id="0d7b0-692">子容器</span><span class="sxs-lookup"><span data-stu-id="0d7b0-692">Child containers</span></span>
* <span data-ttu-id="0d7b0-693">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="0d7b0-693">Custom lifetime management</span></span>
* <span data-ttu-id="0d7b0-694">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="0d7b0-694">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0d7b0-695">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="0d7b0-695">Convention-based registration</span></span>

<span data-ttu-id="0d7b0-696">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-696">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0d7b0-697">Autofac</span><span class="sxs-lookup"><span data-stu-id="0d7b0-697">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0d7b0-698">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0d7b0-698">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0d7b0-699">Grace</span><span class="sxs-lookup"><span data-stu-id="0d7b0-699">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0d7b0-700">LightInject</span><span class="sxs-lookup"><span data-stu-id="0d7b0-700">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0d7b0-701">Lamar</span><span class="sxs-lookup"><span data-stu-id="0d7b0-701">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0d7b0-702">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0d7b0-702">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0d7b0-703">Unity</span><span class="sxs-lookup"><span data-stu-id="0d7b0-703">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0d7b0-704">线程安全</span><span class="sxs-lookup"><span data-stu-id="0d7b0-704">Thread safety</span></span>

<span data-ttu-id="0d7b0-705">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-705">Create thread-safe singleton services.</span></span> <span data-ttu-id="0d7b0-706">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-706">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0d7b0-707">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-707">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0d7b0-708">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-708">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0d7b0-709">建议</span><span class="sxs-lookup"><span data-stu-id="0d7b0-709">Recommendations</span></span>

* <span data-ttu-id="0d7b0-710">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-710">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0d7b0-711">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-711">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0d7b0-712">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-712">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0d7b0-713">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-713">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0d7b0-714">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-714">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0d7b0-715">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-715">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0d7b0-716">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-716">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0d7b0-717">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-717">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0d7b0-718">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-718">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="0d7b0-719">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-719">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="0d7b0-720">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="0d7b0-720">**Incorrect:**</span></span>

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

    <span data-ttu-id="0d7b0-721">**正确**：</span><span class="sxs-lookup"><span data-stu-id="0d7b0-721">**Correct**:</span></span>

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

  * <span data-ttu-id="0d7b0-722">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-722">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="0d7b0-723">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-723">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0d7b0-724">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-724">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0d7b0-725">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-725">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0d7b0-726">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-726">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0d7b0-727">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="0d7b0-727">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d7b0-728">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d7b0-728">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0d7b0-729">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="0d7b0-729">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0d7b0-730">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="0d7b0-730">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="0d7b0-731">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="0d7b0-731">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="0d7b0-732">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0d7b0-732">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0d7b0-733">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="0d7b0-733">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
