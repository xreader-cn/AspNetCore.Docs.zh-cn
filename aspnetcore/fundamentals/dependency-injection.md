---
title: 在 ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 4e990329b7ebcfc9cbbff8a3c9895604a22461d3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661700"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="24c3e-103">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="24c3e-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="24c3e-104">作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="24c3e-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24c3e-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="24c3e-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="24c3e-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="24c3e-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="24c3e-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="24c3e-108">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="24c3e-108">Overview of dependency injection</span></span>

<span data-ttu-id="24c3e-109">依赖项  是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="24c3e-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="24c3e-110">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="24c3e-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="24c3e-111">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="24c3e-112">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="24c3e-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="24c3e-113">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="24c3e-114">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="24c3e-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="24c3e-115">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="24c3e-116">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="24c3e-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="24c3e-117">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="24c3e-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="24c3e-118">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="24c3e-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="24c3e-119">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="24c3e-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="24c3e-120">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="24c3e-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="24c3e-121">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="24c3e-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="24c3e-122">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="24c3e-123">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="24c3e-124">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="24c3e-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="24c3e-125">将服务注入  到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="24c3e-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="24c3e-126">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="24c3e-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="24c3e-127">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="24c3e-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="24c3e-128">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="24c3e-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="24c3e-129">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="24c3e-130">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="24c3e-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="24c3e-131">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="24c3e-132">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="24c3e-133">必须被解析的依赖关系的集合通常被称为“依赖关系树”  、“依赖关系图”  或“对象图”  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="24c3e-134">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="24c3e-135">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="24c3e-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="24c3e-136">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="24c3e-137">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="24c3e-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="24c3e-138">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="24c3e-139">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="24c3e-140">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="24c3e-141">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="24c3e-142">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="24c3e-143">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="24c3e-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="24c3e-144">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="24c3e-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="24c3e-145">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="24c3e-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="24c3e-146">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="24c3e-147">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="24c3e-148">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="24c3e-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="24c3e-149">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-149">Services injected into Startup</span></span>

<span data-ttu-id="24c3e-150">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="24c3e-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="24c3e-151">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="24c3e-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="24c3e-152">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="24c3e-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-153">Framework-provided services</span></span>

<span data-ttu-id="24c3e-154">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="24c3e-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="24c3e-155">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="24c3e-156">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="24c3e-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="24c3e-157">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="24c3e-158">服务类型</span><span class="sxs-lookup"><span data-stu-id="24c3e-158">Service Type</span></span> | <span data-ttu-id="24c3e-159">生存期</span><span class="sxs-lookup"><span data-stu-id="24c3e-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="24c3e-160">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="24c3e-161">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="24c3e-162">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="24c3e-163">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="24c3e-164">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="24c3e-165">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="24c3e-166">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="24c3e-167">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="24c3e-168">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="24c3e-169">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="24c3e-170">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="24c3e-171">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="24c3e-172">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="24c3e-173">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="24c3e-174">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-174">Register additional services with extension methods</span></span>

<span data-ttu-id="24c3e-175">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="24c3e-176">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="24c3e-177">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="24c3e-178">服务生存期</span><span class="sxs-lookup"><span data-stu-id="24c3e-178">Service lifetimes</span></span>

<span data-ttu-id="24c3e-179">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="24c3e-180">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="24c3e-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="24c3e-181">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-181">Transient</span></span>

<span data-ttu-id="24c3e-182">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="24c3e-183">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-183">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="24c3e-184">范围内</span><span class="sxs-lookup"><span data-stu-id="24c3e-184">Scoped</span></span>

<span data-ttu-id="24c3e-185">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="24c3e-185">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="24c3e-186">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="24c3e-186">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="24c3e-187">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="24c3e-187">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="24c3e-188">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-188">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="24c3e-189">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-189">Singleton</span></span>

<span data-ttu-id="24c3e-190">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-190">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="24c3e-191">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-191">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="24c3e-192">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-192">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="24c3e-193">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-193">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="24c3e-194">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="24c3e-194">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="24c3e-195">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="24c3e-195">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="24c3e-196">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="24c3e-196">Service registration methods</span></span>

<span data-ttu-id="24c3e-197">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="24c3e-197">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="24c3e-198">方法</span><span class="sxs-lookup"><span data-stu-id="24c3e-198">Method</span></span> | <span data-ttu-id="24c3e-199">自动</span><span class="sxs-lookup"><span data-stu-id="24c3e-199">Automatic</span></span><br><span data-ttu-id="24c3e-200">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="24c3e-200">object</span></span><br><span data-ttu-id="24c3e-201">处置</span><span class="sxs-lookup"><span data-stu-id="24c3e-201">disposal</span></span> | <span data-ttu-id="24c3e-202">多种</span><span class="sxs-lookup"><span data-stu-id="24c3e-202">Multiple</span></span><br><span data-ttu-id="24c3e-203">实现</span><span class="sxs-lookup"><span data-stu-id="24c3e-203">implementations</span></span> | <span data-ttu-id="24c3e-204">传递参数</span><span class="sxs-lookup"><span data-stu-id="24c3e-204">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="24c3e-205">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-205">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="24c3e-206">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-206">Yes</span></span> | <span data-ttu-id="24c3e-207">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-207">Yes</span></span> | <span data-ttu-id="24c3e-208">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-208">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="24c3e-209">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-209">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="24c3e-210">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-210">Yes</span></span> | <span data-ttu-id="24c3e-211">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-211">Yes</span></span> | <span data-ttu-id="24c3e-212">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="24c3e-213">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-213">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="24c3e-214">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-214">Yes</span></span> | <span data-ttu-id="24c3e-215">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-215">No</span></span> | <span data-ttu-id="24c3e-216">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-216">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="24c3e-217">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-217">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="24c3e-218">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-218">No</span></span> | <span data-ttu-id="24c3e-219">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-219">Yes</span></span> | <span data-ttu-id="24c3e-220">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-220">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="24c3e-221">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-221">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="24c3e-222">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-222">No</span></span> | <span data-ttu-id="24c3e-223">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-223">No</span></span> | <span data-ttu-id="24c3e-224">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-224">Yes</span></span> |

<span data-ttu-id="24c3e-225">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="24c3e-225">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="24c3e-226">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-226">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="24c3e-227">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-227">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="24c3e-228">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-228">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="24c3e-229">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="24c3e-229">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="24c3e-230">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="24c3e-230">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="24c3e-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。 </span><span class="sxs-lookup"><span data-stu-id="24c3e-231">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="24c3e-232">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="24c3e-232">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="24c3e-233">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-233">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="24c3e-234">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="24c3e-234">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="24c3e-235">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-235">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="24c3e-236">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-236">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="24c3e-237">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="24c3e-237">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="24c3e-238">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="24c3e-238">Constructor injection behavior</span></span>

<span data-ttu-id="24c3e-239">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="24c3e-239">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="24c3e-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; 允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="24c3e-240"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="24c3e-241">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="24c3e-241">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="24c3e-242">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="24c3e-242">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="24c3e-243">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-243">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="24c3e-244">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="24c3e-244">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="24c3e-245">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="24c3e-245">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="24c3e-246">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="24c3e-246">Entity Framework contexts</span></span>

<span data-ttu-id="24c3e-247">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="24c3e-247">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="24c3e-248">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="24c3e-248">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="24c3e-249">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="24c3e-249">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="24c3e-250">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="24c3e-250">Lifetime and registration options</span></span>

<span data-ttu-id="24c3e-251">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="24c3e-251">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="24c3e-252">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-252">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="24c3e-253">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="24c3e-253">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="24c3e-254">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="24c3e-254">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="24c3e-255">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="24c3e-255">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="24c3e-256">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-256">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="24c3e-257">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="24c3e-257">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="24c3e-258">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-258">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="24c3e-259">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-259">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="24c3e-260">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="24c3e-260">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="24c3e-261">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="24c3e-261">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="24c3e-262">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="24c3e-262">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="24c3e-263">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="24c3e-263">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="24c3e-264">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-264">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="24c3e-265">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-265">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="24c3e-266">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-266">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="24c3e-267">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-267">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="24c3e-268">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="24c3e-268">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="24c3e-269">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="24c3e-269">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="24c3e-270">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="24c3e-270">**First request:**</span></span>

<span data-ttu-id="24c3e-271">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-271">Controller operations:</span></span>

<span data-ttu-id="24c3e-272">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="24c3e-272">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="24c3e-273">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="24c3e-273">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="24c3e-274">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-274">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-275">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-275">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-276">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-276">`OperationService` operations:</span></span>

<span data-ttu-id="24c3e-277">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="24c3e-277">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="24c3e-278">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="24c3e-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="24c3e-279">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-280">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-281">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="24c3e-281">**Second request:**</span></span>

<span data-ttu-id="24c3e-282">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-282">Controller operations:</span></span>

<span data-ttu-id="24c3e-283">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="24c3e-283">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="24c3e-284">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="24c3e-284">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="24c3e-285">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-285">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-286">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-286">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-287">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-287">`OperationService` operations:</span></span>

<span data-ttu-id="24c3e-288">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="24c3e-288">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="24c3e-289">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="24c3e-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="24c3e-290">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-291">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-292">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="24c3e-292">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="24c3e-293">暂时性  对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="24c3e-293">*Transient* objects are always different.</span></span> <span data-ttu-id="24c3e-294">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-294">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="24c3e-295">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-295">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="24c3e-296">作用域  对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-296">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="24c3e-297">单一实例  对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-297">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="24c3e-298">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-298">Call services from main</span></span>

<span data-ttu-id="24c3e-299">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-299">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="24c3e-300">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-300">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="24c3e-301">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="24c3e-301">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="24c3e-302">作用域验证</span><span class="sxs-lookup"><span data-stu-id="24c3e-302">Scope validation</span></span>

<span data-ttu-id="24c3e-303">如果应用正在开发环境中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="24c3e-303">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="24c3e-304">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-304">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="24c3e-305">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-305">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="24c3e-306">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="24c3e-306">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="24c3e-307">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="24c3e-307">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="24c3e-308">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="24c3e-308">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="24c3e-309">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="24c3e-309">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="24c3e-310">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="24c3e-310">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="24c3e-311">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-311">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="24c3e-312">请求服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-312">Request Services</span></span>

<span data-ttu-id="24c3e-313">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="24c3e-313">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="24c3e-314">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-314">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="24c3e-315">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="24c3e-315">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="24c3e-316">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="24c3e-316">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="24c3e-317">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-317">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="24c3e-318">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="24c3e-318">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="24c3e-319">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="24c3e-319">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="24c3e-320">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-320">Design services for dependency injection</span></span>

<span data-ttu-id="24c3e-321">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="24c3e-321">Best practices are to:</span></span>

* <span data-ttu-id="24c3e-322">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-322">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="24c3e-323">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="24c3e-323">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="24c3e-324">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="24c3e-324">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="24c3e-325">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-325">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="24c3e-326">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="24c3e-326">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="24c3e-327">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="24c3e-327">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="24c3e-328">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-328">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="24c3e-329">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-329">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="24c3e-330">请记住，Razor Pages 页模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="24c3e-330">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="24c3e-331">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="24c3e-331">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="24c3e-332">服务处理</span><span class="sxs-lookup"><span data-stu-id="24c3e-332">Disposal of services</span></span>

<span data-ttu-id="24c3e-333">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-333">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="24c3e-334">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-334">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="24c3e-335">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="24c3e-335">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="24c3e-336">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="24c3e-336">In the following example:</span></span>

* <span data-ttu-id="24c3e-337">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-337">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="24c3e-338">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-338">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="24c3e-339">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-339">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="24c3e-340">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="24c3e-340">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="24c3e-341">有关服务处理选项的讨论，请参阅 [ASP.NET Core 中处理 IDisposable 的四种方法](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-341">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="24c3e-342">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="24c3e-342">Default service container replacement</span></span>

<span data-ttu-id="24c3e-343">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="24c3e-343">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="24c3e-344">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="24c3e-344">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="24c3e-345">属性注入</span><span class="sxs-lookup"><span data-stu-id="24c3e-345">Property injection</span></span>
* <span data-ttu-id="24c3e-346">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="24c3e-346">Injection based on name</span></span>
* <span data-ttu-id="24c3e-347">子容器</span><span class="sxs-lookup"><span data-stu-id="24c3e-347">Child containers</span></span>
* <span data-ttu-id="24c3e-348">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="24c3e-348">Custom lifetime management</span></span>
* <span data-ttu-id="24c3e-349">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="24c3e-349">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="24c3e-350">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="24c3e-350">Convention-based registration</span></span>

<span data-ttu-id="24c3e-351">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="24c3e-351">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="24c3e-352">Autofac</span><span class="sxs-lookup"><span data-stu-id="24c3e-352">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="24c3e-353">DryIoc</span><span class="sxs-lookup"><span data-stu-id="24c3e-353">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="24c3e-354">Grace</span><span class="sxs-lookup"><span data-stu-id="24c3e-354">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="24c3e-355">LightInject</span><span class="sxs-lookup"><span data-stu-id="24c3e-355">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="24c3e-356">Lamar</span><span class="sxs-lookup"><span data-stu-id="24c3e-356">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="24c3e-357">Stashbox</span><span class="sxs-lookup"><span data-stu-id="24c3e-357">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="24c3e-358">Unity</span><span class="sxs-lookup"><span data-stu-id="24c3e-358">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="24c3e-359">线程安全</span><span class="sxs-lookup"><span data-stu-id="24c3e-359">Thread safety</span></span>

<span data-ttu-id="24c3e-360">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-360">Create thread-safe singleton services.</span></span> <span data-ttu-id="24c3e-361">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="24c3e-361">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="24c3e-362">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-362">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="24c3e-363">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="24c3e-363">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="24c3e-364">建议</span><span class="sxs-lookup"><span data-stu-id="24c3e-364">Recommendations</span></span>

* <span data-ttu-id="24c3e-365">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="24c3e-365">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="24c3e-366">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="24c3e-366">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="24c3e-367">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="24c3e-367">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="24c3e-368">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="24c3e-368">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="24c3e-369">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-369">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="24c3e-370">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="24c3e-370">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="24c3e-371">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="24c3e-371">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="24c3e-372">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-372">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="24c3e-373">避免使用服务定位器模式  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-373">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="24c3e-374">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-374">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="24c3e-375">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="24c3e-375">**Incorrect:**</span></span>

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

  <span data-ttu-id="24c3e-376">**正确**：</span><span class="sxs-lookup"><span data-stu-id="24c3e-376">**Correct**:</span></span>

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

* <span data-ttu-id="24c3e-377">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="24c3e-377">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="24c3e-378">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="24c3e-378">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="24c3e-379">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-379">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="24c3e-380">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="24c3e-380">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="24c3e-381">例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="24c3e-381">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="24c3e-382">DI 是静态/全局对象访问模式的替代方法  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-382">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="24c3e-383">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="24c3e-383">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24c3e-384">其他资源</span><span class="sxs-lookup"><span data-stu-id="24c3e-384">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="24c3e-385">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="24c3e-385">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="24c3e-386">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="24c3e-386">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="24c3e-387">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="24c3e-387">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="24c3e-388">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-388">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24c3e-389">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="24c3e-389">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="24c3e-390">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-390">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="24c3e-391">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="24c3e-391">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="24c3e-392">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="24c3e-392">Overview of dependency injection</span></span>

<span data-ttu-id="24c3e-393">依赖项  是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="24c3e-393">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="24c3e-394">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="24c3e-394">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="24c3e-395">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-395">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="24c3e-396">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="24c3e-396">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="24c3e-397">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-397">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="24c3e-398">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="24c3e-398">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="24c3e-399">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-399">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="24c3e-400">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="24c3e-400">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="24c3e-401">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="24c3e-401">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="24c3e-402">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="24c3e-402">This implementation is difficult to unit test.</span></span> <span data-ttu-id="24c3e-403">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="24c3e-403">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="24c3e-404">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="24c3e-404">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="24c3e-405">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="24c3e-405">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="24c3e-406">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-406">Registration of the dependency in a service container.</span></span> <span data-ttu-id="24c3e-407">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-407">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="24c3e-408">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="24c3e-408">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="24c3e-409">将服务注入  到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="24c3e-409">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="24c3e-410">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="24c3e-410">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="24c3e-411">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="24c3e-411">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="24c3e-412">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="24c3e-412">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="24c3e-413">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-413">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="24c3e-414">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="24c3e-414">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="24c3e-415">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-415">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="24c3e-416">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-416">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="24c3e-417">必须被解析的依赖关系的集合通常被称为“依赖关系树”  、“依赖关系图”  或“对象图”  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-417">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="24c3e-418">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-418">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="24c3e-419">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="24c3e-419">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="24c3e-420">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-420">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="24c3e-421">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="24c3e-421">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="24c3e-422">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-422">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="24c3e-423">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-423">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="24c3e-424">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-424">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="24c3e-425">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-425">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="24c3e-426">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-426">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="24c3e-427">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="24c3e-427">We recommended that apps follow this convention.</span></span> <span data-ttu-id="24c3e-428">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="24c3e-428">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="24c3e-429">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="24c3e-429">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="24c3e-430">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-430">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="24c3e-431">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-431">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="24c3e-432">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="24c3e-432">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="24c3e-433">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-433">Services injected into Startup</span></span>

<span data-ttu-id="24c3e-434">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="24c3e-434">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="24c3e-435">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="24c3e-435">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="24c3e-436">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-436">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="24c3e-437">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-437">Framework-provided services</span></span>

<span data-ttu-id="24c3e-438">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="24c3e-438">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="24c3e-439">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-439">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="24c3e-440">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="24c3e-440">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="24c3e-441">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-441">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="24c3e-442">服务类型</span><span class="sxs-lookup"><span data-stu-id="24c3e-442">Service Type</span></span> | <span data-ttu-id="24c3e-443">生存期</span><span class="sxs-lookup"><span data-stu-id="24c3e-443">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="24c3e-444">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-444">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="24c3e-445">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-445">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="24c3e-446">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="24c3e-447">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-447">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="24c3e-448">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-448">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="24c3e-449">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-449">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="24c3e-450">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-450">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="24c3e-451">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-451">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="24c3e-452">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-452">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="24c3e-453">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-453">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="24c3e-454">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-454">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="24c3e-455">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-455">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="24c3e-456">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-456">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="24c3e-457">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-457">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="24c3e-458">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-458">Register additional services with extension methods</span></span>

<span data-ttu-id="24c3e-459">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-459">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="24c3e-460">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-460">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="24c3e-461">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-461">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="24c3e-462">服务生存期</span><span class="sxs-lookup"><span data-stu-id="24c3e-462">Service lifetimes</span></span>

<span data-ttu-id="24c3e-463">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-463">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="24c3e-464">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="24c3e-464">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="24c3e-465">暂时</span><span class="sxs-lookup"><span data-stu-id="24c3e-465">Transient</span></span>

<span data-ttu-id="24c3e-466">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-466">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="24c3e-467">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-467">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="24c3e-468">范围内</span><span class="sxs-lookup"><span data-stu-id="24c3e-468">Scoped</span></span>

<span data-ttu-id="24c3e-469">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="24c3e-469">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="24c3e-470">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="24c3e-470">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="24c3e-471">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="24c3e-471">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="24c3e-472">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-472">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="24c3e-473">单例</span><span class="sxs-lookup"><span data-stu-id="24c3e-473">Singleton</span></span>

<span data-ttu-id="24c3e-474">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-474">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="24c3e-475">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-475">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="24c3e-476">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-476">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="24c3e-477">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-477">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="24c3e-478">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="24c3e-478">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="24c3e-479">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="24c3e-479">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="24c3e-480">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="24c3e-480">Service registration methods</span></span>

<span data-ttu-id="24c3e-481">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="24c3e-481">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="24c3e-482">方法</span><span class="sxs-lookup"><span data-stu-id="24c3e-482">Method</span></span> | <span data-ttu-id="24c3e-483">自动</span><span class="sxs-lookup"><span data-stu-id="24c3e-483">Automatic</span></span><br><span data-ttu-id="24c3e-484">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="24c3e-484">object</span></span><br><span data-ttu-id="24c3e-485">处置</span><span class="sxs-lookup"><span data-stu-id="24c3e-485">disposal</span></span> | <span data-ttu-id="24c3e-486">多种</span><span class="sxs-lookup"><span data-stu-id="24c3e-486">Multiple</span></span><br><span data-ttu-id="24c3e-487">实现</span><span class="sxs-lookup"><span data-stu-id="24c3e-487">implementations</span></span> | <span data-ttu-id="24c3e-488">传递参数</span><span class="sxs-lookup"><span data-stu-id="24c3e-488">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="24c3e-489">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-489">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="24c3e-490">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-490">Yes</span></span> | <span data-ttu-id="24c3e-491">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-491">Yes</span></span> | <span data-ttu-id="24c3e-492">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-492">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="24c3e-493">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-493">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="24c3e-494">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-494">Yes</span></span> | <span data-ttu-id="24c3e-495">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-495">Yes</span></span> | <span data-ttu-id="24c3e-496">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-496">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="24c3e-497">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-497">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="24c3e-498">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-498">Yes</span></span> | <span data-ttu-id="24c3e-499">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-499">No</span></span> | <span data-ttu-id="24c3e-500">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-500">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="24c3e-501">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-501">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="24c3e-502">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-502">No</span></span> | <span data-ttu-id="24c3e-503">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-503">Yes</span></span> | <span data-ttu-id="24c3e-504">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-504">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="24c3e-505">示例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-505">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="24c3e-506">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-506">No</span></span> | <span data-ttu-id="24c3e-507">否</span><span class="sxs-lookup"><span data-stu-id="24c3e-507">No</span></span> | <span data-ttu-id="24c3e-508">是</span><span class="sxs-lookup"><span data-stu-id="24c3e-508">Yes</span></span> |

<span data-ttu-id="24c3e-509">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="24c3e-509">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="24c3e-510">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-510">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="24c3e-511">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-511">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="24c3e-512">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-512">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="24c3e-513">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="24c3e-513">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="24c3e-514">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="24c3e-514">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="24c3e-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。 </span><span class="sxs-lookup"><span data-stu-id="24c3e-515">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="24c3e-516">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="24c3e-516">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="24c3e-517">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-517">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="24c3e-518">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="24c3e-518">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="24c3e-519">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-519">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="24c3e-520">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-520">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="24c3e-521">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="24c3e-521">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="24c3e-522">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="24c3e-522">Constructor injection behavior</span></span>

<span data-ttu-id="24c3e-523">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="24c3e-523">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="24c3e-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; 允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="24c3e-524"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="24c3e-525">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="24c3e-525">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="24c3e-526">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="24c3e-526">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="24c3e-527">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-527">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="24c3e-528">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="24c3e-528">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="24c3e-529">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="24c3e-529">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="24c3e-530">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="24c3e-530">Entity Framework contexts</span></span>

<span data-ttu-id="24c3e-531">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="24c3e-531">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="24c3e-532">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="24c3e-532">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="24c3e-533">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="24c3e-533">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="24c3e-534">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="24c3e-534">Lifetime and registration options</span></span>

<span data-ttu-id="24c3e-535">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="24c3e-535">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="24c3e-536">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-536">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="24c3e-537">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="24c3e-537">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="24c3e-538">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="24c3e-538">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="24c3e-539">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="24c3e-539">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="24c3e-540">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-540">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="24c3e-541">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="24c3e-541">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="24c3e-542">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-542">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="24c3e-543">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-543">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="24c3e-544">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="24c3e-544">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="24c3e-545">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="24c3e-545">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="24c3e-546">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="24c3e-546">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="24c3e-547">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="24c3e-547">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="24c3e-548">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-548">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="24c3e-549">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-549">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="24c3e-550">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-550">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="24c3e-551">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="24c3e-551">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="24c3e-552">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="24c3e-552">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="24c3e-553">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="24c3e-553">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="24c3e-554">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="24c3e-554">**First request:**</span></span>

<span data-ttu-id="24c3e-555">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-555">Controller operations:</span></span>

<span data-ttu-id="24c3e-556">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="24c3e-556">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="24c3e-557">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="24c3e-557">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="24c3e-558">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-558">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-559">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-559">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-560">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-560">`OperationService` operations:</span></span>

<span data-ttu-id="24c3e-561">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="24c3e-561">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="24c3e-562">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="24c3e-562">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="24c3e-563">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-563">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-564">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-564">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-565">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="24c3e-565">**Second request:**</span></span>

<span data-ttu-id="24c3e-566">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-566">Controller operations:</span></span>

<span data-ttu-id="24c3e-567">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="24c3e-567">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="24c3e-568">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="24c3e-568">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="24c3e-569">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-569">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-570">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-570">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-571">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="24c3e-571">`OperationService` operations:</span></span>

<span data-ttu-id="24c3e-572">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="24c3e-572">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="24c3e-573">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="24c3e-573">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="24c3e-574">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="24c3e-574">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="24c3e-575">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="24c3e-575">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="24c3e-576">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="24c3e-576">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="24c3e-577">暂时性  对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="24c3e-577">*Transient* objects are always different.</span></span> <span data-ttu-id="24c3e-578">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-578">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="24c3e-579">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-579">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="24c3e-580">作用域  对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-580">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="24c3e-581">单一实例  对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-581">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="24c3e-582">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-582">Call services from main</span></span>

<span data-ttu-id="24c3e-583">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-583">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="24c3e-584">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-584">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="24c3e-585">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="24c3e-585">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="24c3e-586">作用域验证</span><span class="sxs-lookup"><span data-stu-id="24c3e-586">Scope validation</span></span>

<span data-ttu-id="24c3e-587">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="24c3e-587">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="24c3e-588">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-588">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="24c3e-589">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-589">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="24c3e-590">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="24c3e-590">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="24c3e-591">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="24c3e-591">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="24c3e-592">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="24c3e-592">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="24c3e-593">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="24c3e-593">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="24c3e-594">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="24c3e-594">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="24c3e-595">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-595">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="24c3e-596">请求服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-596">Request Services</span></span>

<span data-ttu-id="24c3e-597">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="24c3e-597">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="24c3e-598">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-598">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="24c3e-599">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="24c3e-599">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="24c3e-600">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="24c3e-600">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="24c3e-601">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-601">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="24c3e-602">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="24c3e-602">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="24c3e-603">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="24c3e-603">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="24c3e-604">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-604">Design services for dependency injection</span></span>

<span data-ttu-id="24c3e-605">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="24c3e-605">Best practices are to:</span></span>

* <span data-ttu-id="24c3e-606">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="24c3e-606">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="24c3e-607">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="24c3e-607">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="24c3e-608">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="24c3e-608">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="24c3e-609">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-609">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="24c3e-610">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="24c3e-610">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="24c3e-611">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="24c3e-611">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="24c3e-612">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-612">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="24c3e-613">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="24c3e-613">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="24c3e-614">请记住，Razor Pages 页模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="24c3e-614">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="24c3e-615">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="24c3e-615">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="24c3e-616">服务处理</span><span class="sxs-lookup"><span data-stu-id="24c3e-616">Disposal of services</span></span>

<span data-ttu-id="24c3e-617">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="24c3e-617">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="24c3e-618">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="24c3e-618">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="24c3e-619">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="24c3e-619">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="24c3e-620">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="24c3e-620">In the following example:</span></span>

* <span data-ttu-id="24c3e-621">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-621">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="24c3e-622">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="24c3e-622">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="24c3e-623">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-623">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="24c3e-624">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="24c3e-624">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="24c3e-625">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="24c3e-625">Default service container replacement</span></span>

<span data-ttu-id="24c3e-626">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="24c3e-626">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="24c3e-627">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="24c3e-627">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="24c3e-628">属性注入</span><span class="sxs-lookup"><span data-stu-id="24c3e-628">Property injection</span></span>
* <span data-ttu-id="24c3e-629">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="24c3e-629">Injection based on name</span></span>
* <span data-ttu-id="24c3e-630">子容器</span><span class="sxs-lookup"><span data-stu-id="24c3e-630">Child containers</span></span>
* <span data-ttu-id="24c3e-631">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="24c3e-631">Custom lifetime management</span></span>
* <span data-ttu-id="24c3e-632">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="24c3e-632">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="24c3e-633">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="24c3e-633">Convention-based registration</span></span>

<span data-ttu-id="24c3e-634">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="24c3e-634">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="24c3e-635">Autofac</span><span class="sxs-lookup"><span data-stu-id="24c3e-635">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="24c3e-636">DryIoc</span><span class="sxs-lookup"><span data-stu-id="24c3e-636">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="24c3e-637">Grace</span><span class="sxs-lookup"><span data-stu-id="24c3e-637">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="24c3e-638">LightInject</span><span class="sxs-lookup"><span data-stu-id="24c3e-638">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="24c3e-639">Lamar</span><span class="sxs-lookup"><span data-stu-id="24c3e-639">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="24c3e-640">Stashbox</span><span class="sxs-lookup"><span data-stu-id="24c3e-640">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="24c3e-641">Unity</span><span class="sxs-lookup"><span data-stu-id="24c3e-641">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="24c3e-642">线程安全</span><span class="sxs-lookup"><span data-stu-id="24c3e-642">Thread safety</span></span>

<span data-ttu-id="24c3e-643">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="24c3e-643">Create thread-safe singleton services.</span></span> <span data-ttu-id="24c3e-644">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="24c3e-644">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="24c3e-645">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="24c3e-645">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="24c3e-646">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="24c3e-646">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="24c3e-647">建议</span><span class="sxs-lookup"><span data-stu-id="24c3e-647">Recommendations</span></span>

* <span data-ttu-id="24c3e-648">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="24c3e-648">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="24c3e-649">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="24c3e-649">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="24c3e-650">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="24c3e-650">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="24c3e-651">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="24c3e-651">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="24c3e-652">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="24c3e-652">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="24c3e-653">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="24c3e-653">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="24c3e-654">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="24c3e-654">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="24c3e-655">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-655">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="24c3e-656">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-656">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="24c3e-657">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="24c3e-657">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="24c3e-658">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="24c3e-658">**Incorrect:**</span></span>

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

    <span data-ttu-id="24c3e-659">**正确**：</span><span class="sxs-lookup"><span data-stu-id="24c3e-659">**Correct**:</span></span>

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

  * <span data-ttu-id="24c3e-660">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="24c3e-660">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="24c3e-661">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="24c3e-661">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="24c3e-662">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="24c3e-662">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="24c3e-663">例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="24c3e-663">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="24c3e-664">DI 是静态/全局对象访问模式的替代方法  。</span><span class="sxs-lookup"><span data-stu-id="24c3e-664">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="24c3e-665">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="24c3e-665">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24c3e-666">其他资源</span><span class="sxs-lookup"><span data-stu-id="24c3e-666">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="24c3e-667">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="24c3e-667">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="24c3e-668">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="24c3e-668">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="24c3e-669">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="24c3e-669">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="24c3e-670">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="24c3e-670">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
