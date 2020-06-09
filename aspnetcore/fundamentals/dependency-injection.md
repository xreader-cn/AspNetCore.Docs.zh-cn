---
title: 'title:ASP.NET Core 依赖关系注入 author: rick-anderson description:了解 ASP.NET Core 如何实现依赖注入和如何使用它。'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date:2020 年 5 月 14 日 no-loc:"
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
ms.openlocfilehash: db0a23e2db34de60308ea9be021a190278dee4aa
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271898"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="854b7-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="854b7-103">'Blazor'</span></span>

<span data-ttu-id="854b7-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="854b7-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="854b7-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="854b7-105">'Let's Encrypt'</span></span>

<span data-ttu-id="854b7-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="854b7-106">'Razor'</span></span>

<span data-ttu-id="854b7-107">'SignalR' uid: fundamentals/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="854b7-107">'SignalR' uid: fundamentals/dependency-injection</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="854b7-108">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="854b7-108">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="854b7-109">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="854b7-109">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span> <span data-ttu-id="854b7-110">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="854b7-110">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="854b7-111">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="854b7-111">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="854b7-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="854b7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="854b7-113">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="854b7-113">Overview of dependency injection</span></span> <span data-ttu-id="854b7-114">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="854b7-114">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="854b7-115">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="854b7-115">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="854b7-116">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="854b7-116">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="854b7-117">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="854b7-117">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="854b7-118">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-118">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="854b7-119">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="854b7-119">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="854b7-120">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="854b7-120">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="854b7-121">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="854b7-121">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="854b7-122">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="854b7-122">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="854b7-123">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="854b7-123">This implementation is difficult to unit test.</span></span> <span data-ttu-id="854b7-124">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-124">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="854b7-125">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="854b7-125">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="854b7-126">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="854b7-126">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="854b7-127">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-127">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="854b7-128">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="854b7-128">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="854b7-129">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="854b7-129">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="854b7-130">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="854b7-130">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="854b7-131">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="854b7-131">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="854b7-132">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="854b7-132">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="854b7-133">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="854b7-133">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="854b7-134">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="854b7-134">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="854b7-135">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="854b7-135">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="854b7-136">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-136">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="854b7-137">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-137">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="854b7-138">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="854b7-138">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="854b7-139">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="854b7-139">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="854b7-140">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="854b7-140">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="854b7-141">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="854b7-141">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="854b7-142">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="854b7-142">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="854b7-143">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-143">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="854b7-144">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-144">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="854b7-145">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="854b7-145">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="854b7-146">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-146">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="854b7-147">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-147">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="854b7-148">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="854b7-148">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="854b7-149">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="854b7-149">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="854b7-150">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="854b7-150">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="854b7-151">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-151">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="854b7-152">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-152">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="854b7-153">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="854b7-153">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="854b7-154">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="854b7-154">Services injected into Startup</span></span> <span data-ttu-id="854b7-155">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="854b7-155">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="854b7-156">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="854b7-156">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="854b7-157">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="854b7-157">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="854b7-158">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="854b7-158">Framework-provided services</span></span> | <span data-ttu-id="854b7-159">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="854b7-159">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="854b7-160">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="854b7-160">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="854b7-161">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="854b7-161">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="854b7-162">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="854b7-162">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="854b7-163">服务类型</span><span class="sxs-lookup"><span data-stu-id="854b7-163">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="854b7-164">生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-164">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="854b7-165">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-165">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="854b7-166">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-166">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="854b7-167">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="854b7-168">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="854b7-169">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-169">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="854b7-170">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-170">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="854b7-171">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-171">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="854b7-172">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="854b7-173">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="854b7-174">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-174">Singleton</span></span>

<span data-ttu-id="854b7-175">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-175">Transient</span></span> <span data-ttu-id="854b7-176">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-176">Singleton</span></span>

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

<span data-ttu-id="854b7-177">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-177">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="854b7-178">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-178">Singleton</span></span>

<span data-ttu-id="854b7-179">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="854b7-179">Register additional services with extension methods</span></span> <span data-ttu-id="854b7-180">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-180">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="854b7-181">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-181">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="854b7-182">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="854b7-182">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="854b7-183">服务生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-183">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="854b7-184">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-184">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="854b7-185">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="854b7-185">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="854b7-186">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-186">Transient</span></span> <span data-ttu-id="854b7-187">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="854b7-187">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="854b7-188">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-188">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="854b7-189">范围内</span><span class="sxs-lookup"><span data-stu-id="854b7-189">Scoped</span></span>

<span data-ttu-id="854b7-190">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="854b7-190">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="854b7-191">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-191">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="854b7-192">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="854b7-192">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="854b7-193">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="854b7-193">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="854b7-194">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-194">Singleton</span></span> <span data-ttu-id="854b7-195">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="854b7-195">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="854b7-196">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-196">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="854b7-197">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-197">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="854b7-198">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-198">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="854b7-199">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="854b7-199">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="854b7-200">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="854b7-200">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="854b7-201">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="854b7-201">Service registration methods</span></span> | <span data-ttu-id="854b7-202">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="854b7-202">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="854b7-203">方法</span><span class="sxs-lookup"><span data-stu-id="854b7-203">Method</span></span> | <span data-ttu-id="854b7-204">自动</span><span class="sxs-lookup"><span data-stu-id="854b7-204">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="854b7-205">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="854b7-205">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="854b7-206">处置</span><span class="sxs-lookup"><span data-stu-id="854b7-206">disposal</span></span> | <span data-ttu-id="854b7-207">多种</span><span class="sxs-lookup"><span data-stu-id="854b7-207">Multiple</span></span> | <span data-ttu-id="854b7-208">实现</span><span class="sxs-lookup"><span data-stu-id="854b7-208">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="854b7-209">传递参数</span><span class="sxs-lookup"><span data-stu-id="854b7-209">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="854b7-210">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-210">Example:</span></span> | <span data-ttu-id="854b7-211">是</span><span class="sxs-lookup"><span data-stu-id="854b7-211">Yes</span></span> | <span data-ttu-id="854b7-212">是</span><span class="sxs-lookup"><span data-stu-id="854b7-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="854b7-213">否</span><span class="sxs-lookup"><span data-stu-id="854b7-213">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="854b7-214">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-214">Examples:</span></span> | <span data-ttu-id="854b7-215">是</span><span class="sxs-lookup"><span data-stu-id="854b7-215">Yes</span></span> | <span data-ttu-id="854b7-216">是</span><span class="sxs-lookup"><span data-stu-id="854b7-216">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="854b7-217">是</span><span class="sxs-lookup"><span data-stu-id="854b7-217">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="854b7-218">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-218">Example:</span></span> | <span data-ttu-id="854b7-219">是</span><span class="sxs-lookup"><span data-stu-id="854b7-219">Yes</span></span> | <span data-ttu-id="854b7-220">否</span><span class="sxs-lookup"><span data-stu-id="854b7-220">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="854b7-221">否</span><span class="sxs-lookup"><span data-stu-id="854b7-221">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="854b7-222">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-222">Examples:</span></span> | <span data-ttu-id="854b7-223">否</span><span class="sxs-lookup"><span data-stu-id="854b7-223">No</span></span> | <span data-ttu-id="854b7-224">是</span><span class="sxs-lookup"><span data-stu-id="854b7-224">Yes</span></span> |

<span data-ttu-id="854b7-225">是</span><span class="sxs-lookup"><span data-stu-id="854b7-225">Yes</span></span> <span data-ttu-id="854b7-226">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-226">Examples:</span></span>

<span data-ttu-id="854b7-227">否</span><span class="sxs-lookup"><span data-stu-id="854b7-227">No</span></span>

<span data-ttu-id="854b7-228">否</span><span class="sxs-lookup"><span data-stu-id="854b7-228">No</span></span> <span data-ttu-id="854b7-229">是</span><span class="sxs-lookup"><span data-stu-id="854b7-229">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="854b7-230">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="854b7-230">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="854b7-231">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="854b7-231">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="854b7-232">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-232">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="854b7-233">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="854b7-233">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="854b7-234">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="854b7-234">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="854b7-235">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="854b7-235">For more information, see:</span></span> <span data-ttu-id="854b7-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="854b7-237">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="854b7-237">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="854b7-238">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-238">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="854b7-239">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="854b7-239">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="854b7-240">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="854b7-240">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="854b7-241">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="854b7-241">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="854b7-242">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="854b7-242">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="854b7-243">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="854b7-243">Constructor injection behavior</span></span>

<span data-ttu-id="854b7-244">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="854b7-244">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="854b7-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="854b7-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="854b7-246">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="854b7-246">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="854b7-247">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="854b7-247">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="854b7-248">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="854b7-248">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="854b7-249">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="854b7-249">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="854b7-250">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="854b7-250">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="854b7-251">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="854b7-251">Entity Framework contexts</span></span> <span data-ttu-id="854b7-252">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="854b7-252">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="854b7-253">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="854b7-253">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="854b7-254">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="854b7-254">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="854b7-255">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="854b7-255">Lifetime and registration options</span></span> <span data-ttu-id="854b7-256">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="854b7-256">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="854b7-257">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="854b7-257">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="854b7-258">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="854b7-258">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="854b7-259">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="854b7-259">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="854b7-260">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="854b7-260">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="854b7-261">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-261">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="854b7-262">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="854b7-262">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="854b7-263">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-263">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="854b7-264">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="854b7-264">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="854b7-265">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="854b7-265">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="854b7-266">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="854b7-266">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="854b7-267">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="854b7-267">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="854b7-268">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="854b7-268">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="854b7-269">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-269">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="854b7-270">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="854b7-270">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="854b7-271">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-271">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="854b7-272">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="854b7-272">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="854b7-273">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="854b7-273">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="854b7-274">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="854b7-274">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="854b7-275">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="854b7-275">**First request:**</span></span>

<span data-ttu-id="854b7-276">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-276">Controller operations:</span></span>

<span data-ttu-id="854b7-277">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="854b7-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="854b7-278">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="854b7-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="854b7-279">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="854b7-280">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="854b7-281">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-281">`OperationService` operations:</span></span>

<span data-ttu-id="854b7-282">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="854b7-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="854b7-283">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="854b7-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="854b7-284">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="854b7-285">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-285">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="854b7-286">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="854b7-286">**Second request:**</span></span>

<span data-ttu-id="854b7-287">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-287">Controller operations:</span></span>

<span data-ttu-id="854b7-288">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="854b7-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="854b7-289">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="854b7-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="854b7-290">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="854b7-291">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="854b7-292">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-292">`OperationService` operations:</span></span>

* <span data-ttu-id="854b7-293">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="854b7-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="854b7-294">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="854b7-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="854b7-295">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="854b7-296">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="854b7-297">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="854b7-297">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="854b7-298">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="854b7-298">*Transient* objects are always different.</span></span>

<span data-ttu-id="854b7-299">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="854b7-299">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="854b7-300">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-300">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="854b7-301">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="854b7-301">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="854b7-302">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="854b7-302">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="854b7-303">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="854b7-303">Call services from main</span></span>

* <span data-ttu-id="854b7-304">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-304">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="854b7-305">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="854b7-305">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="854b7-306">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="854b7-306">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="854b7-307">作用域验证</span><span class="sxs-lookup"><span data-stu-id="854b7-307">Scope validation</span></span>

<span data-ttu-id="854b7-308">如果应用正在开发环境中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="854b7-308">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="854b7-309">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-309">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="854b7-310">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-310">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="854b7-311">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="854b7-311">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="854b7-312">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="854b7-312">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="854b7-313">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="854b7-313">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="854b7-314">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="854b7-314">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="854b7-315">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="854b7-315">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="854b7-316">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="854b7-316">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="854b7-317">请求服务</span><span class="sxs-lookup"><span data-stu-id="854b7-317">Request Services</span></span> <span data-ttu-id="854b7-318">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="854b7-318">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="854b7-319">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-319">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="854b7-320">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="854b7-320">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="854b7-321">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="854b7-321">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="854b7-322">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-322">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span>
* <span data-ttu-id="854b7-323">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="854b7-323">This yields classes that are easier to test.</span></span> <span data-ttu-id="854b7-324">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="854b7-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="854b7-325">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="854b7-325">Design services for dependency injection</span></span> <span data-ttu-id="854b7-326">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="854b7-326">Best practices are to:</span></span>
* <span data-ttu-id="854b7-327">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-327">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="854b7-328">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="854b7-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="854b7-329">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="854b7-329">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="854b7-330">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="854b7-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="854b7-331">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="854b7-331">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="854b7-332">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="854b7-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="854b7-333">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="854b7-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="854b7-334">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="854b7-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="854b7-335">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="854b7-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="854b7-336">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="854b7-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="854b7-337">服务处理</span><span class="sxs-lookup"><span data-stu-id="854b7-337">Disposal of services</span></span>
* <span data-ttu-id="854b7-338">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="854b7-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="854b7-339">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="854b7-340">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="854b7-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="854b7-341">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="854b7-341">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="854b7-342">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="854b7-342">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="854b7-343">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-343">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="854b7-344">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-344">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="854b7-345">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="854b7-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="854b7-346">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="854b7-346">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="854b7-347">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-347">Transient, limited lifetime</span></span>

<span data-ttu-id="854b7-348">**方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-348">**Scenario**</span></span> <span data-ttu-id="854b7-349">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="854b7-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="854b7-350">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-350">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="854b7-351">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-351">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="854b7-352">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-352">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="854b7-353">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-353">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="854b7-354">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="854b7-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="854b7-355">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="854b7-355">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="854b7-356">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="854b7-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="854b7-357">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="854b7-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="854b7-358">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-358">Shared Instance, limited lifetime</span></span> <span data-ttu-id="854b7-359">**方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-359">**Scenario**</span></span> <span data-ttu-id="854b7-360">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="854b7-361">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-361">**Solution**</span></span>

* <span data-ttu-id="854b7-362">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="854b7-363">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="854b7-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="854b7-364">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="854b7-365">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="854b7-365">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="854b7-366">通用准则</span><span class="sxs-lookup"><span data-stu-id="854b7-366">General Guidelines</span></span> <span data-ttu-id="854b7-367">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="854b7-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="854b7-368">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="854b7-368">Use the factory pattern instead.</span></span> <span data-ttu-id="854b7-369">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="854b7-370">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="854b7-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="854b7-371">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="854b7-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="854b7-372"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="854b7-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="854b7-373">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-373">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="854b7-374">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="854b7-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="854b7-375">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="854b7-375">Default service container replacement</span></span>
* <span data-ttu-id="854b7-376">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="854b7-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="854b7-377">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="854b7-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="854b7-378">属性注入</span><span class="sxs-lookup"><span data-stu-id="854b7-378">Property injection</span></span>

<span data-ttu-id="854b7-379">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="854b7-379">Injection based on name</span></span>

* <span data-ttu-id="854b7-380">子容器</span><span class="sxs-lookup"><span data-stu-id="854b7-380">Child containers</span></span>
* <span data-ttu-id="854b7-381">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="854b7-381">Custom lifetime management</span></span>
* <span data-ttu-id="854b7-382">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="854b7-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="854b7-383">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="854b7-383">Convention-based registration</span></span>
* <span data-ttu-id="854b7-384">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="854b7-384">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="854b7-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="854b7-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="854b7-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="854b7-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="854b7-387">Grace</span><span class="sxs-lookup"><span data-stu-id="854b7-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="854b7-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="854b7-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="854b7-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="854b7-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="854b7-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="854b7-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="854b7-391">Unity</span><span class="sxs-lookup"><span data-stu-id="854b7-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="854b7-392">线程安全</span><span class="sxs-lookup"><span data-stu-id="854b7-392">Thread safety</span></span>

* <span data-ttu-id="854b7-393">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="854b7-394">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="854b7-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="854b7-395">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="854b7-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="854b7-396">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="854b7-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="854b7-397">建议</span><span class="sxs-lookup"><span data-stu-id="854b7-397">Recommendations</span></span> <span data-ttu-id="854b7-398">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="854b7-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="854b7-399">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="854b7-400">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="854b7-400">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="854b7-401">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="854b7-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="854b7-402">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="854b7-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

  <span data-ttu-id="854b7-403">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="854b7-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

  <span data-ttu-id="854b7-404">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="854b7-404">It's better to request the actual item via DI.</span></span>

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

* <span data-ttu-id="854b7-405">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="854b7-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span> <span data-ttu-id="854b7-406">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="854b7-406">Avoid using the *service locator pattern*.</span></span>

* <span data-ttu-id="854b7-407">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="854b7-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

<span data-ttu-id="854b7-408">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="854b7-408">**Incorrect:**</span></span> <span data-ttu-id="854b7-409">**正确**：</span><span class="sxs-lookup"><span data-stu-id="854b7-409">**Correct**:</span></span>

<span data-ttu-id="854b7-410">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="854b7-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="854b7-411">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="854b7-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="854b7-412">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="854b7-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="854b7-413">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="854b7-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="854b7-414">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="854b7-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="854b7-415">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-415">DI is an *alternative* to static/global object access patterns.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="854b7-416">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="854b7-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="854b7-417">DI 中多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="854b7-417">Recommended patterns for multi-tenancy in DI</span></span>
* <span data-ttu-id="854b7-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="854b7-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span>
* <span data-ttu-id="854b7-419">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="854b7-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>
* <span data-ttu-id="854b7-420">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="854b7-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>
* <span data-ttu-id="854b7-421">其他资源</span><span class="sxs-lookup"><span data-stu-id="854b7-421">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[<span data-ttu-id="854b7-422">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="854b7-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)

[<span data-ttu-id="854b7-423">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="854b7-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)

<span data-ttu-id="854b7-424">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="854b7-424">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>

## <a name="overview-of-dependency-injection"></a>[<span data-ttu-id="854b7-425">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="854b7-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)

[<span data-ttu-id="854b7-426">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="854b7-426">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) <span data-ttu-id="854b7-427">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="854b7-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="854b7-428">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="854b7-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="854b7-429">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="854b7-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="854b7-430">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="854b7-430">Overview of dependency injection</span></span> <span data-ttu-id="854b7-431">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="854b7-431">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="854b7-432">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="854b7-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="854b7-433">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="854b7-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="854b7-434">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="854b7-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="854b7-435">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="854b7-436">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="854b7-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="854b7-437">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="854b7-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="854b7-438">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="854b7-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="854b7-439">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="854b7-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="854b7-440">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="854b7-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="854b7-441">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="854b7-442">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="854b7-442">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="854b7-443">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="854b7-443">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="854b7-444">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-444">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="854b7-445">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="854b7-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="854b7-446">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="854b7-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="854b7-447">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="854b7-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="854b7-448">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="854b7-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="854b7-449">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="854b7-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="854b7-450">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="854b7-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="854b7-451">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="854b7-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="854b7-452">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="854b7-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="854b7-453">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-453">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="854b7-454">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="854b7-455">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="854b7-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="854b7-456">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="854b7-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="854b7-457">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="854b7-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="854b7-458">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="854b7-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="854b7-459">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="854b7-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="854b7-460">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="854b7-461">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="854b7-462">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="854b7-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="854b7-463">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="854b7-464">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="854b7-465">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="854b7-465">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="854b7-466">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="854b7-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="854b7-467">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="854b7-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="854b7-468">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="854b7-469">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-469">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="854b7-470">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="854b7-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="854b7-471">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="854b7-471">Services injected into Startup</span></span> <span data-ttu-id="854b7-472">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="854b7-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="854b7-473">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="854b7-473">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="854b7-474">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="854b7-474">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="854b7-475">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="854b7-475">Framework-provided services</span></span> | <span data-ttu-id="854b7-476">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="854b7-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="854b7-477">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="854b7-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="854b7-478">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="854b7-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="854b7-479">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="854b7-479">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="854b7-480">服务类型</span><span class="sxs-lookup"><span data-stu-id="854b7-480">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="854b7-481">生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-481">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="854b7-482">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="854b7-483">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-483">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="854b7-484">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="854b7-485">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="854b7-486">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="854b7-487">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="854b7-488">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-488">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="854b7-489">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="854b7-490">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="854b7-491">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-491">Singleton</span></span>

<span data-ttu-id="854b7-492">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-492">Transient</span></span> <span data-ttu-id="854b7-493">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-493">Singleton</span></span>

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

<span data-ttu-id="854b7-494">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-494">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="854b7-495">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-495">Singleton</span></span>

<span data-ttu-id="854b7-496">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="854b7-496">Register additional services with extension methods</span></span> <span data-ttu-id="854b7-497">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="854b7-498">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="854b7-499">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="854b7-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="854b7-500">服务生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-500">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="854b7-501">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-501">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="854b7-502">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="854b7-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="854b7-503">暂时</span><span class="sxs-lookup"><span data-stu-id="854b7-503">Transient</span></span> <span data-ttu-id="854b7-504">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="854b7-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="854b7-505">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-505">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="854b7-506">范围内</span><span class="sxs-lookup"><span data-stu-id="854b7-506">Scoped</span></span>

<span data-ttu-id="854b7-507">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="854b7-507">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="854b7-508">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="854b7-509">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="854b7-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="854b7-510">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="854b7-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="854b7-511">单例</span><span class="sxs-lookup"><span data-stu-id="854b7-511">Singleton</span></span> <span data-ttu-id="854b7-512">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="854b7-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="854b7-513">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-513">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="854b7-514">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="854b7-515">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="854b7-516">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="854b7-516">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="854b7-517">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="854b7-517">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="854b7-518">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="854b7-518">Service registration methods</span></span> | <span data-ttu-id="854b7-519">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="854b7-519">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="854b7-520">方法</span><span class="sxs-lookup"><span data-stu-id="854b7-520">Method</span></span> | <span data-ttu-id="854b7-521">自动</span><span class="sxs-lookup"><span data-stu-id="854b7-521">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="854b7-522">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="854b7-522">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="854b7-523">处置</span><span class="sxs-lookup"><span data-stu-id="854b7-523">disposal</span></span> | <span data-ttu-id="854b7-524">多种</span><span class="sxs-lookup"><span data-stu-id="854b7-524">Multiple</span></span> | <span data-ttu-id="854b7-525">实现</span><span class="sxs-lookup"><span data-stu-id="854b7-525">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="854b7-526">传递参数</span><span class="sxs-lookup"><span data-stu-id="854b7-526">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="854b7-527">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-527">Example:</span></span> | <span data-ttu-id="854b7-528">是</span><span class="sxs-lookup"><span data-stu-id="854b7-528">Yes</span></span> | <span data-ttu-id="854b7-529">是</span><span class="sxs-lookup"><span data-stu-id="854b7-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="854b7-530">否</span><span class="sxs-lookup"><span data-stu-id="854b7-530">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="854b7-531">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-531">Examples:</span></span> | <span data-ttu-id="854b7-532">是</span><span class="sxs-lookup"><span data-stu-id="854b7-532">Yes</span></span> | <span data-ttu-id="854b7-533">是</span><span class="sxs-lookup"><span data-stu-id="854b7-533">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="854b7-534">是</span><span class="sxs-lookup"><span data-stu-id="854b7-534">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="854b7-535">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-535">Example:</span></span> | <span data-ttu-id="854b7-536">是</span><span class="sxs-lookup"><span data-stu-id="854b7-536">Yes</span></span> | <span data-ttu-id="854b7-537">否</span><span class="sxs-lookup"><span data-stu-id="854b7-537">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="854b7-538">否</span><span class="sxs-lookup"><span data-stu-id="854b7-538">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="854b7-539">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-539">Examples:</span></span> | <span data-ttu-id="854b7-540">否</span><span class="sxs-lookup"><span data-stu-id="854b7-540">No</span></span> | <span data-ttu-id="854b7-541">是</span><span class="sxs-lookup"><span data-stu-id="854b7-541">Yes</span></span> |

<span data-ttu-id="854b7-542">是</span><span class="sxs-lookup"><span data-stu-id="854b7-542">Yes</span></span> <span data-ttu-id="854b7-543">示例：</span><span class="sxs-lookup"><span data-stu-id="854b7-543">Examples:</span></span>

<span data-ttu-id="854b7-544">否</span><span class="sxs-lookup"><span data-stu-id="854b7-544">No</span></span>

<span data-ttu-id="854b7-545">否</span><span class="sxs-lookup"><span data-stu-id="854b7-545">No</span></span> <span data-ttu-id="854b7-546">是</span><span class="sxs-lookup"><span data-stu-id="854b7-546">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="854b7-547">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="854b7-547">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="854b7-548">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="854b7-548">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="854b7-549">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-549">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="854b7-550">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="854b7-550">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="854b7-551">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="854b7-551">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="854b7-552">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="854b7-552">For more information, see:</span></span> <span data-ttu-id="854b7-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="854b7-554">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="854b7-554">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="854b7-555">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-555">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="854b7-556">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="854b7-556">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="854b7-557">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="854b7-557">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="854b7-558">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="854b7-558">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="854b7-559">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="854b7-559">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="854b7-560">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="854b7-560">Constructor injection behavior</span></span>

<span data-ttu-id="854b7-561">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="854b7-561">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="854b7-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="854b7-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="854b7-563">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="854b7-563">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="854b7-564">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="854b7-564">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="854b7-565">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="854b7-565">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="854b7-566">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="854b7-566">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="854b7-567">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="854b7-567">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="854b7-568">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="854b7-568">Entity Framework contexts</span></span> <span data-ttu-id="854b7-569">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="854b7-569">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="854b7-570">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="854b7-570">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="854b7-571">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="854b7-571">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="854b7-572">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="854b7-572">Lifetime and registration options</span></span> <span data-ttu-id="854b7-573">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="854b7-573">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="854b7-574">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="854b7-574">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="854b7-575">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="854b7-575">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="854b7-576">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="854b7-576">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="854b7-577">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="854b7-577">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="854b7-578">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-578">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="854b7-579">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="854b7-579">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="854b7-580">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-580">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="854b7-581">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="854b7-581">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="854b7-582">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="854b7-582">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="854b7-583">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="854b7-583">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="854b7-584">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="854b7-584">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="854b7-585">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="854b7-585">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="854b7-586">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-586">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="854b7-587">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="854b7-587">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="854b7-588">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-588">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="854b7-589">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="854b7-589">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="854b7-590">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="854b7-590">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="854b7-591">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="854b7-591">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="854b7-592">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="854b7-592">**First request:**</span></span>

<span data-ttu-id="854b7-593">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-593">Controller operations:</span></span>

<span data-ttu-id="854b7-594">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="854b7-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="854b7-595">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="854b7-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="854b7-596">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="854b7-597">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="854b7-598">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-598">`OperationService` operations:</span></span>

<span data-ttu-id="854b7-599">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="854b7-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="854b7-600">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="854b7-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="854b7-601">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="854b7-602">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-602">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="854b7-603">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="854b7-603">**Second request:**</span></span>

<span data-ttu-id="854b7-604">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-604">Controller operations:</span></span>

<span data-ttu-id="854b7-605">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="854b7-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="854b7-606">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="854b7-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="854b7-607">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="854b7-608">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="854b7-609">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="854b7-609">`OperationService` operations:</span></span>

* <span data-ttu-id="854b7-610">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="854b7-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="854b7-611">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="854b7-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="854b7-612">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="854b7-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="854b7-613">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="854b7-613">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="854b7-614">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="854b7-614">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="854b7-615">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="854b7-615">*Transient* objects are always different.</span></span>

<span data-ttu-id="854b7-616">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="854b7-616">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="854b7-617">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-617">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="854b7-618">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="854b7-618">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="854b7-619">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="854b7-619">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="854b7-620">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="854b7-620">Call services from main</span></span>

* <span data-ttu-id="854b7-621">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-621">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="854b7-622">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="854b7-622">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="854b7-623">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="854b7-623">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="854b7-624">作用域验证</span><span class="sxs-lookup"><span data-stu-id="854b7-624">Scope validation</span></span>

<span data-ttu-id="854b7-625">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="854b7-625">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="854b7-626">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-626">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="854b7-627">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-627">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="854b7-628">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="854b7-628">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="854b7-629">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="854b7-629">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="854b7-630">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="854b7-630">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="854b7-631">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="854b7-631">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="854b7-632">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="854b7-632">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="854b7-633">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="854b7-633">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="854b7-634">请求服务</span><span class="sxs-lookup"><span data-stu-id="854b7-634">Request Services</span></span> <span data-ttu-id="854b7-635">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="854b7-635">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="854b7-636">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-636">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="854b7-637">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="854b7-637">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="854b7-638">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="854b7-638">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="854b7-639">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-639">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span>
* <span data-ttu-id="854b7-640">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="854b7-640">This yields classes that are easier to test.</span></span> <span data-ttu-id="854b7-641">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="854b7-641">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="854b7-642">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="854b7-642">Design services for dependency injection</span></span> <span data-ttu-id="854b7-643">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="854b7-643">Best practices are to:</span></span>
* <span data-ttu-id="854b7-644">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="854b7-644">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="854b7-645">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="854b7-645">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="854b7-646">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="854b7-646">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="854b7-647">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="854b7-647">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="854b7-648">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="854b7-648">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="854b7-649">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="854b7-649">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="854b7-650">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="854b7-650">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="854b7-651">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="854b7-651">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="854b7-652">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="854b7-652">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="854b7-653">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="854b7-653">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="854b7-654">服务处理</span><span class="sxs-lookup"><span data-stu-id="854b7-654">Disposal of services</span></span>
* <span data-ttu-id="854b7-655">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="854b7-655">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="854b7-656">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-656">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="854b7-657">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="854b7-657">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="854b7-658">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="854b7-658">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="854b7-659">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="854b7-659">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="854b7-660">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-660">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="854b7-661">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-661">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="854b7-662">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="854b7-662">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="854b7-663">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="854b7-663">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="854b7-664">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-664">Transient, limited lifetime</span></span>

<span data-ttu-id="854b7-665">**方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-665">**Scenario**</span></span> <span data-ttu-id="854b7-666">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="854b7-666">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="854b7-667">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-667">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="854b7-668">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-668">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="854b7-669">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-669">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="854b7-670">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-670">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="854b7-671">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="854b7-671">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="854b7-672">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="854b7-672">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="854b7-673">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="854b7-673">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="854b7-674">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="854b7-674">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="854b7-675">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="854b7-675">Shared Instance, limited lifetime</span></span> <span data-ttu-id="854b7-676">**方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-676">**Scenario**</span></span> <span data-ttu-id="854b7-677">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-677">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="854b7-678">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="854b7-678">**Solution**</span></span>

* <span data-ttu-id="854b7-679">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-679">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="854b7-680">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="854b7-680">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="854b7-681">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-681">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="854b7-682">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="854b7-682">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="854b7-683">通用准则</span><span class="sxs-lookup"><span data-stu-id="854b7-683">General Guidelines</span></span> <span data-ttu-id="854b7-684">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="854b7-684">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="854b7-685">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="854b7-685">Use the factory pattern instead.</span></span> <span data-ttu-id="854b7-686">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="854b7-686">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="854b7-687">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="854b7-687">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="854b7-688">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="854b7-688">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="854b7-689"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="854b7-689">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="854b7-690">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="854b7-690">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="854b7-691">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="854b7-691">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="854b7-692">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="854b7-692">Default service container replacement</span></span>
* <span data-ttu-id="854b7-693">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="854b7-693">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="854b7-694">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="854b7-694">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="854b7-695">属性注入</span><span class="sxs-lookup"><span data-stu-id="854b7-695">Property injection</span></span>

<span data-ttu-id="854b7-696">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="854b7-696">Injection based on name</span></span>

* <span data-ttu-id="854b7-697">子容器</span><span class="sxs-lookup"><span data-stu-id="854b7-697">Child containers</span></span>
* <span data-ttu-id="854b7-698">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="854b7-698">Custom lifetime management</span></span>
* <span data-ttu-id="854b7-699">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="854b7-699">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="854b7-700">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="854b7-700">Convention-based registration</span></span>
* <span data-ttu-id="854b7-701">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="854b7-701">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="854b7-702">Autofac</span><span class="sxs-lookup"><span data-stu-id="854b7-702">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="854b7-703">DryIoc</span><span class="sxs-lookup"><span data-stu-id="854b7-703">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="854b7-704">Grace</span><span class="sxs-lookup"><span data-stu-id="854b7-704">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="854b7-705">LightInject</span><span class="sxs-lookup"><span data-stu-id="854b7-705">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="854b7-706">Lamar</span><span class="sxs-lookup"><span data-stu-id="854b7-706">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="854b7-707">Stashbox</span><span class="sxs-lookup"><span data-stu-id="854b7-707">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="854b7-708">Unity</span><span class="sxs-lookup"><span data-stu-id="854b7-708">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="854b7-709">线程安全</span><span class="sxs-lookup"><span data-stu-id="854b7-709">Thread safety</span></span>

* <span data-ttu-id="854b7-710">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="854b7-710">Create thread-safe singleton services.</span></span> <span data-ttu-id="854b7-711">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="854b7-711">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="854b7-712">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="854b7-712">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="854b7-713">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="854b7-713">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="854b7-714">建议</span><span class="sxs-lookup"><span data-stu-id="854b7-714">Recommendations</span></span> <span data-ttu-id="854b7-715">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="854b7-715">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="854b7-716">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-716">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="854b7-717">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="854b7-717">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="854b7-718">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="854b7-718">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span>

  * <span data-ttu-id="854b7-719">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="854b7-719">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

    <span data-ttu-id="854b7-720">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="854b7-720">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

    <span data-ttu-id="854b7-721">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="854b7-721">It's better to request the actual item via DI.</span></span>

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

  * <span data-ttu-id="854b7-722">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="854b7-722">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="854b7-723">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="854b7-723">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

<span data-ttu-id="854b7-724">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="854b7-724">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span> <span data-ttu-id="854b7-725">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="854b7-725">**Incorrect:**</span></span>

<span data-ttu-id="854b7-726">**正确**：</span><span class="sxs-lookup"><span data-stu-id="854b7-726">**Correct**:</span></span> <span data-ttu-id="854b7-727">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="854b7-727">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="854b7-728">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="854b7-728">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="854b7-729">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="854b7-729">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span>
* <span data-ttu-id="854b7-730">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="854b7-730">Exceptions are rare, mostly special cases within the framework itself.</span></span>
* <span data-ttu-id="854b7-731">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="854b7-731">DI is an *alternative* to static/global object access patterns.</span></span>
* <span data-ttu-id="854b7-732">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="854b7-732">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>
* <span data-ttu-id="854b7-733">其他资源</span><span class="sxs-lookup"><span data-stu-id="854b7-733">Additional resources</span></span>

::: moniker-end
