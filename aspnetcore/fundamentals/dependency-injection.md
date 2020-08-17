---
title: ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130628"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="dfa31-103">ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="dfa31-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dfa31-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="dfa31-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="dfa31-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="dfa31-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="dfa31-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="dfa31-107">有关选项的依赖项注入的详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="dfa31-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="dfa31-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="dfa31-109">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="dfa31-109">Overview of dependency injection</span></span>

<span data-ttu-id="dfa31-110">依赖项是指另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="dfa31-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="dfa31-111">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="dfa31-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="dfa31-112">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="dfa31-113">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="dfa31-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="dfa31-114">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="dfa31-115">代码依赖项（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="dfa31-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="dfa31-116">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="dfa31-117">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="dfa31-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="dfa31-118">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="dfa31-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="dfa31-119">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="dfa31-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="dfa31-120">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="dfa31-121">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="dfa31-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="dfa31-122">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="dfa31-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="dfa31-123">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="dfa31-124">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="dfa31-125">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="dfa31-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="dfa31-126">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="dfa31-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="dfa31-127">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="dfa31-128">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="dfa31-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="dfa31-129">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="dfa31-130">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="dfa31-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 方法使用范围内生存期（单个请求的生存期）注册服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="dfa31-132">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="dfa31-133">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="dfa31-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="dfa31-134">使用 DI 模式：</span><span class="sxs-lookup"><span data-stu-id="dfa31-134">With the DI pattern:</span></span>

* <span data-ttu-id="dfa31-135">控制器不使用具体类型 `MyDependency`，仅使用 `IMyDependency` 接口。</span><span class="sxs-lookup"><span data-stu-id="dfa31-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="dfa31-136">这样可以轻松地更改控制器使用的实现，而无需修改控制器。</span><span class="sxs-lookup"><span data-stu-id="dfa31-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="dfa31-137">控制器不创建 `MyDependency` 的实例，它由 DI 容器创建。</span><span class="sxs-lookup"><span data-stu-id="dfa31-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="dfa31-138">可以通过使用内置日志记录 API 来改善 `IMyDependency` 接口的实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="dfa31-139">更新的 `ConfigureServices` 方法注册新的 `IMyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="dfa31-140">`MyDependency2` 请求构造函数中的 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="dfa31-141">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="dfa31-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="dfa31-142">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="dfa31-143">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="dfa31-144">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="dfa31-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="dfa31-145">`ILogger<TCategoryName>` 是[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="dfa31-146">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="dfa31-147">在依赖项注入术语中，服务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="dfa31-148">通常是向应用中其他代码提供服务的对象，如 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="dfa31-149">与 Web 服务无关，尽管服务可能使用 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="dfa31-150">框架提供可靠的[日志记录](xref:fundamentals/logging/index)系统。</span><span class="sxs-lookup"><span data-stu-id="dfa31-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="dfa31-151">编写 `IMyDependency` 实现来演示基本的 DI，而不是来实现日志记录。</span><span class="sxs-lookup"><span data-stu-id="dfa31-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="dfa31-152">大多数应用都不需要编写记录器。</span><span class="sxs-lookup"><span data-stu-id="dfa31-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="dfa31-153">下面的代码演示如何使用默认日志记录，这不要求在 `ConfigureServices` 中注册任何服务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="dfa31-154">使用前面的代码时，无需更新 `ConfigureServices`，因为框架提供[日志记录](xref:fundamentals/logging/index)：</span><span class="sxs-lookup"><span data-stu-id="dfa31-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="dfa31-155">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-155">Services injected into Startup</span></span>

<span data-ttu-id="dfa31-156">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="dfa31-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="dfa31-157">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="dfa31-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="dfa31-158">有关详细信息，请参阅 <xref:fundamentals/startup> 和[访问 Startup 中的配置](xref:fundamentals/configuration/index#access-configuration-in-startup)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="dfa31-159">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-159">Register additional services with extension methods</span></span>

<span data-ttu-id="dfa31-160">当服务集合扩展方法可用于注册服务时：</span><span class="sxs-lookup"><span data-stu-id="dfa31-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="dfa31-161">约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="dfa31-162">也会注册从属服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-162">The dependent services are also registered.</span></span>

<span data-ttu-id="dfa31-163">下面的代码通过个人用户帐户由 Razor 页面模板生成，并演示如何使用扩展方法 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> 将其他服务添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="dfa31-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="dfa31-164">有关详细信息，请参阅 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 和 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="dfa31-165">有关编写用于注册服务的扩展方法的说明，请参阅[合并服务集合](#csc)部分。</span><span class="sxs-lookup"><span data-stu-id="dfa31-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="dfa31-166">服务生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-166">Service lifetimes</span></span>

<span data-ttu-id="dfa31-167">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="dfa31-168">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="dfa31-169">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-169">Transient</span></span>

<span data-ttu-id="dfa31-170">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="dfa31-171">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="dfa31-172">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="dfa31-173">作用域</span><span class="sxs-lookup"><span data-stu-id="dfa31-173">Scoped</span></span>

<span data-ttu-id="dfa31-174">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="dfa31-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="dfa31-175">使用 Entity Framework Core 时，默认情况下 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展方法使用范围内生存期来注册 `DbContext` 类型。</span><span class="sxs-lookup"><span data-stu-id="dfa31-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="dfa31-176">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="dfa31-177">通过以下方法之一在中间件中使用范围内服务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="dfa31-178">将服务注入 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="dfa31-179">通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入会在运行时引发异常，因为它强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="dfa31-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="dfa31-180">[生存期和注册选项](#lifetime-and-registration-options)中的示例使用 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="dfa31-181">[基于工厂的中间件](<xref:fundamentals/middleware/extensibility>)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="dfa31-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 扩展方法检查中间件的已注册类型是否实现 <xref:Microsoft.AspNetCore.Http.IMiddleware>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="dfa31-183">如果是，则使用在容器中注册的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实例来解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 实现，而不使用基于约定的中间件激活逻辑。</span><span class="sxs-lookup"><span data-stu-id="dfa31-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="dfa31-184">中间件在应用的服务容器中注册为作用域或瞬态服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="dfa31-185">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="dfa31-186">不要从单一实例解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="dfa31-187">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="dfa31-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="dfa31-188">可以：</span><span class="sxs-lookup"><span data-stu-id="dfa31-188">It's fine to:</span></span>

* <span data-ttu-id="dfa31-189">从范围内或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="dfa31-190">从其他范围内或暂时性服务解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="dfa31-191">默认情况下在开发环境中，从具有较长生存期的其他服务解析服务将引发异常。</span><span class="sxs-lookup"><span data-stu-id="dfa31-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="dfa31-192">有关详细信息，请参阅[作用域验证](#sv)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="dfa31-193">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-193">Singleton</span></span>

<span data-ttu-id="dfa31-194">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)：</span><span class="sxs-lookup"><span data-stu-id="dfa31-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="dfa31-195">在首次请求它们时进行创建；或者</span><span class="sxs-lookup"><span data-stu-id="dfa31-195">The first time they're requested.</span></span>
* <span data-ttu-id="dfa31-196">在向容器直接提供实现实例时由开发人员进行创建。</span><span class="sxs-lookup"><span data-stu-id="dfa31-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="dfa31-197">很少用到此方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-197">This approach is rarely needed.</span></span>

<span data-ttu-id="dfa31-198">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="dfa31-199">如果应用需要单一实例行为，则允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="dfa31-200">不要实现单一实例设计模式，或提供代码来释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="dfa31-201">服务永远不应由解析容器服务的代码释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="dfa31-202">如果类型或工厂注册为单一实例，则容器将自动释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="dfa31-203">单一实例服务必须是线程安全的，并且通常在无状态服务中使用。</span><span class="sxs-lookup"><span data-stu-id="dfa31-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="dfa31-204">在处理请求的应用中，如果在应用程序关闭时释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="dfa31-205">由于应用关闭之前不释放内存，因此必须考虑单一实例的内存使用。</span><span class="sxs-lookup"><span data-stu-id="dfa31-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="dfa31-206">不要从单一实例解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="dfa31-207">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="dfa31-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="dfa31-208">可以从范围内或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="dfa31-209">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="dfa31-209">Service registration methods</span></span>

<span data-ttu-id="dfa31-210">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="dfa31-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="dfa31-211">方法</span><span class="sxs-lookup"><span data-stu-id="dfa31-211">Method</span></span> | <span data-ttu-id="dfa31-212">自动</span><span class="sxs-lookup"><span data-stu-id="dfa31-212">Automatic</span></span><br><span data-ttu-id="dfa31-213">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="dfa31-213">object</span></span><br><span data-ttu-id="dfa31-214">处置</span><span class="sxs-lookup"><span data-stu-id="dfa31-214">disposal</span></span> | <span data-ttu-id="dfa31-215">多种</span><span class="sxs-lookup"><span data-stu-id="dfa31-215">Multiple</span></span><br><span data-ttu-id="dfa31-216">实现</span><span class="sxs-lookup"><span data-stu-id="dfa31-216">implementations</span></span> | <span data-ttu-id="dfa31-217">传递参数</span><span class="sxs-lookup"><span data-stu-id="dfa31-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="dfa31-218">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="dfa31-219">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-219">Yes</span></span> | <span data-ttu-id="dfa31-220">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-220">Yes</span></span> | <span data-ttu-id="dfa31-221">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="dfa31-222">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="dfa31-223">“是”</span><span class="sxs-lookup"><span data-stu-id="dfa31-223">Yes</span></span> | <span data-ttu-id="dfa31-224">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-224">Yes</span></span> | <span data-ttu-id="dfa31-225">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="dfa31-226">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="dfa31-227">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-227">Yes</span></span> | <span data-ttu-id="dfa31-228">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-228">No</span></span> | <span data-ttu-id="dfa31-229">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="dfa31-230">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="dfa31-231">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-231">No</span></span> | <span data-ttu-id="dfa31-232">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-232">Yes</span></span> | <span data-ttu-id="dfa31-233">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="dfa31-234">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="dfa31-235">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-235">No</span></span> | <span data-ttu-id="dfa31-236">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-236">No</span></span> | <span data-ttu-id="dfa31-237">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-237">Yes</span></span> |

<span data-ttu-id="dfa31-238">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="dfa31-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="dfa31-239">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="dfa31-240">`TryAdd{LIFETIME}` 方法在尚未注册实现情况下注册该服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="dfa31-241">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="dfa31-242">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="dfa31-243">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="dfa31-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="dfa31-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法在没有同一类型实现的情况下注册该服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="dfa31-245">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="dfa31-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="dfa31-246">注册服务时，开发人员应在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="dfa31-247">通常库作者使用 `TryAddEnumerable` 来避免在容器中注册实现的多个副本。</span><span class="sxs-lookup"><span data-stu-id="dfa31-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="dfa31-248">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="dfa31-249">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="dfa31-250">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="dfa31-251">服务注册通常与顺序无关，除了注册同一类型的多个实现时。</span><span class="sxs-lookup"><span data-stu-id="dfa31-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="dfa31-252">`IServiceCollection` 是 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 的集合。</span><span class="sxs-lookup"><span data-stu-id="dfa31-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="dfa31-253">下面的代码演示如何使用构造函数添加服务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="dfa31-254">`Add{LIFETIME}` 方法使用相同的方式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="dfa31-255">相关示例请参阅 [AddScoped 的源代码](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="dfa31-256">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="dfa31-256">Constructor injection behavior</span></span>

<span data-ttu-id="dfa31-257">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="dfa31-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="dfa31-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="dfa31-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="dfa31-259">在依赖项注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="dfa31-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="dfa31-260">与框架功能一起使用，例如[标记帮助程序](xref:mvc/views/tag-helpers/intro)、[MVC 控制器](xref:mvc/models/model-binding)和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="dfa31-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="dfa31-261">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="dfa31-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="dfa31-262">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="dfa31-263">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="dfa31-264">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="dfa31-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="dfa31-265">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="dfa31-265">Entity Framework contexts</span></span>

<span data-ttu-id="dfa31-266">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="dfa31-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="dfa31-267">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="dfa31-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="dfa31-268">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="dfa31-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="dfa31-269">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="dfa31-269">Lifetime and registration options</span></span>

<span data-ttu-id="dfa31-270">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="dfa31-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="dfa31-271">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="dfa31-272">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="dfa31-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="dfa31-273">`Operation` 构造函数生成 GUID 的最后 4 个字符（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="dfa31-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="dfa31-274">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="dfa31-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="dfa31-275">示例应用演示了请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="dfa31-276">示例应用的 `IndexModel` 和中间件请求每种 `IOperation` 类型并记录 `OperationId`：</span><span class="sxs-lookup"><span data-stu-id="dfa31-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="dfa31-277">中间件与 `IndexModel` 类似，并解析相同的服务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="dfa31-278">范围内服务必须在 `InvokeAsync` 方法中进行解析：</span><span class="sxs-lookup"><span data-stu-id="dfa31-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="dfa31-279">记录器输出显示：</span><span class="sxs-lookup"><span data-stu-id="dfa31-279">The logger output shows:</span></span>

* <span data-ttu-id="dfa31-280">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="dfa31-280">*Transient* objects are always different.</span></span> <span data-ttu-id="dfa31-281">`IndexModel` 和中间件中的临时 `OperationId` 值不同。</span><span class="sxs-lookup"><span data-stu-id="dfa31-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="dfa31-282">范围内对象在每个请求中是相同的，但在请求之间不同。</span><span class="sxs-lookup"><span data-stu-id="dfa31-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="dfa31-283">单一实例对象对于每个请求是相同的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="dfa31-284">若要减少日志记录输出，请在 appsettings.Development.json 文件中设置“Logging:LogLevel:Microsoft:Error”：</span><span class="sxs-lookup"><span data-stu-id="dfa31-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="dfa31-285">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-285">Call services from main</span></span>

<span data-ttu-id="dfa31-286">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="dfa31-287">此方法可用于在启动时访问范围内服务以运行初始化任务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="dfa31-288">前面的代码源自 Razor 页面教程的[添加种子初始值设定项](xref:tutorials/razor-pages/sql?#add-the-seed-initializer)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="dfa31-289">以下示例演示如何在 `Program.Main` 中获取 `IMyDependency` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="dfa31-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="dfa31-290">作用域验证</span><span class="sxs-lookup"><span data-stu-id="dfa31-290">Scope validation</span></span>

<span data-ttu-id="dfa31-291">如果应用正在[开发环境](xref:fundamentals/environments)中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 以生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="dfa31-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="dfa31-292">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="dfa31-293">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="dfa31-294">未将暂时性服务直接或间接注入单一实例或范围内服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="dfa31-295">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="dfa31-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="dfa31-296">在启动提供程序和应用时，根服务提供程序的生存期对应于应用的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="dfa31-297">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="dfa31-298">如果范围内服务创建于根容器，则该服务的生存期实际上提升至单一实例，因为根容器只会在应用关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="dfa31-299">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="dfa31-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="dfa31-300">有关详细信息，请参阅[作用域验证](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="dfa31-301">请求服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-301">Request Services</span></span>

<span data-ttu-id="dfa31-302">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="dfa31-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="dfa31-303">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="dfa31-304">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="dfa31-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="dfa31-305">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="dfa31-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="dfa31-306">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="dfa31-307">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="dfa31-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="dfa31-308">ASP.NET Core 为每个请求创建一个范围，`RequestServices` 公开范围内服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="dfa31-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="dfa31-309">只要请求处于活动状态，所有范围内的服务都有效。</span><span class="sxs-lookup"><span data-stu-id="dfa31-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="dfa31-310">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="dfa31-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="dfa31-311">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-311">Design services for dependency injection</span></span>

<span data-ttu-id="dfa31-312">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="dfa31-312">Best practices are to:</span></span>

* <span data-ttu-id="dfa31-313">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="dfa31-314">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="dfa31-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="dfa31-315">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="dfa31-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="dfa31-316">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="dfa31-317">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="dfa31-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="dfa31-318">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="dfa31-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="dfa31-319">如果一个类似乎有过多的注入依赖项，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="dfa31-320">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="dfa31-321">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="dfa31-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="dfa31-322">服务释放</span><span class="sxs-lookup"><span data-stu-id="dfa31-322">Disposal of services</span></span>

<span data-ttu-id="dfa31-323">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="dfa31-324">服务永远不应由任何解析容器服务的代码释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="dfa31-325">如果类型或工厂注册为单一实例，则容器释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="dfa31-326">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="dfa31-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="dfa31-327">每次刷新索引页后，调试控制台显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="dfa31-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="dfa31-328">不由服务容器创建的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="dfa31-329">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="dfa31-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="dfa31-330">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="dfa31-330">In the preceding code:</span></span>

* <span data-ttu-id="dfa31-331">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="dfa31-332">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="dfa31-333">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="dfa31-334">服务如果未通过开发者代码显式释放，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="dfa31-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="dfa31-335">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="dfa31-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="dfa31-336">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-336">Transient, limited lifetime</span></span>

<span data-ttu-id="dfa31-337">**方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-337">**Scenario**</span></span>

<span data-ttu-id="dfa31-338">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="dfa31-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="dfa31-339">在根范围（根容器）内解析实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="dfa31-340">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="dfa31-341">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-341">**Solution**</span></span>

<span data-ttu-id="dfa31-342">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="dfa31-343">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="dfa31-344">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="dfa31-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="dfa31-345">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="dfa31-346">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="dfa31-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="dfa31-347">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="dfa31-348">**方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-348">**Scenario**</span></span>

<span data-ttu-id="dfa31-349">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="dfa31-350">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-350">**Solution**</span></span>

<span data-ttu-id="dfa31-351">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="dfa31-352">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="dfa31-353">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="dfa31-354">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="dfa31-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="dfa31-355">一般 IDisposable 准则</span><span class="sxs-lookup"><span data-stu-id="dfa31-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="dfa31-356">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="dfa31-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="dfa31-357">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="dfa31-358">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="dfa31-359">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="dfa31-360">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="dfa31-361"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="dfa31-362">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="dfa31-363">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="dfa31-364">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="dfa31-364">Default service container replacement</span></span>

<span data-ttu-id="dfa31-365">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="dfa31-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="dfa31-366">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="dfa31-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="dfa31-367">属性注入</span><span class="sxs-lookup"><span data-stu-id="dfa31-367">Property injection</span></span>
* <span data-ttu-id="dfa31-368">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="dfa31-368">Injection based on name</span></span>
* <span data-ttu-id="dfa31-369">子容器</span><span class="sxs-lookup"><span data-stu-id="dfa31-369">Child containers</span></span>
* <span data-ttu-id="dfa31-370">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="dfa31-370">Custom lifetime management</span></span>
* <span data-ttu-id="dfa31-371">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="dfa31-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="dfa31-372">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="dfa31-372">Convention-based registration</span></span>

<span data-ttu-id="dfa31-373">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="dfa31-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="dfa31-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="dfa31-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="dfa31-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="dfa31-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="dfa31-376">Grace</span><span class="sxs-lookup"><span data-stu-id="dfa31-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="dfa31-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="dfa31-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="dfa31-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="dfa31-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="dfa31-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="dfa31-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="dfa31-380">Unity</span><span class="sxs-lookup"><span data-stu-id="dfa31-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="dfa31-381">线程安全</span><span class="sxs-lookup"><span data-stu-id="dfa31-381">Thread safety</span></span>

<span data-ttu-id="dfa31-382">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="dfa31-383">如果单例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="dfa31-384">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="dfa31-385">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="dfa31-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="dfa31-386">建议</span><span class="sxs-lookup"><span data-stu-id="dfa31-386">Recommendations</span></span>

* <span data-ttu-id="dfa31-387">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="dfa31-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="dfa31-388">C# 不支持异步构造函数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="dfa31-389">建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="dfa31-390">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="dfa31-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="dfa31-391">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="dfa31-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="dfa31-392">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="dfa31-393">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="dfa31-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="dfa31-394">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="dfa31-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="dfa31-395">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-395">Avoid static access to services.</span></span> <span data-ttu-id="dfa31-396">例如，避免静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="dfa31-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="dfa31-397">使 DI 工厂保持快速且同步。</span><span class="sxs-lookup"><span data-stu-id="dfa31-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="dfa31-398">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="dfa31-399">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="dfa31-400">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="dfa31-400">**Incorrect:**</span></span>

    ![错误代码](dependency-injection/_static/bad.png)

  <span data-ttu-id="dfa31-402">**正确**：</span><span class="sxs-lookup"><span data-stu-id="dfa31-402">**Correct**:</span></span>

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
* <span data-ttu-id="dfa31-403">要避免的另一个服务定位器变体是注入需在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="dfa31-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="dfa31-404">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="dfa31-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="dfa31-405">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="dfa31-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="dfa31-406">避免在 `ConfigureServices` 中调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="dfa31-407">当开发人员想要在 `ConfigureServices` 中解析服务时，通常会调用 `BuildServiceProvider`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="dfa31-408">例如，假设需要从配置中获取 `LoginPath`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="dfa31-409">避免以下代码：</span><span class="sxs-lookup"><span data-stu-id="dfa31-409">Avoid the following code:</span></span>

  ![调用 BuildServiceProvider 的错误代码](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="dfa31-411">在上图中，选择 `services.BuildServiceProvider` 下的绿色波浪线将显示以下 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="dfa31-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="dfa31-412">ASP0000 从应用程序代码调用“BuildServiceProvider”会导致创建单一实例服务的其他副本。</span><span class="sxs-lookup"><span data-stu-id="dfa31-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="dfa31-413">考虑依赖项注入服务等替代项作为“Configure”的参数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="dfa31-414">调用 `BuildServiceProvider` 会创建第二个容器，该容器可创建残缺的单一实例并导致跨多个容器引用对象图。</span><span class="sxs-lookup"><span data-stu-id="dfa31-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="dfa31-415">获取 `LoginPath` 的正确方法是将选项模式用于 DI：</span><span class="sxs-lookup"><span data-stu-id="dfa31-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="dfa31-416">可释放的暂时性服务由容器捕获以进行释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="dfa31-417">如果从顶级容器解析，这会变为内存泄漏。</span><span class="sxs-lookup"><span data-stu-id="dfa31-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="dfa31-418">启用范围验证，确保应用不具有捕获单一实例的范围内服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="dfa31-419">有关详细信息，请参阅[作用域验证](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="dfa31-420">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="dfa31-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="dfa31-421">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="dfa31-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="dfa31-422">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="dfa31-423">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="dfa31-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="dfa31-424">DI 中适用于多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="dfa31-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="dfa31-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="dfa31-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="dfa31-426">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="dfa31-427">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="dfa31-428">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-428">Framework-provided services</span></span>

<span data-ttu-id="dfa31-429">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="dfa31-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="dfa31-430">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="dfa31-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="dfa31-431">基于 ASP.NET Core 模板的应用包含 250 个以上由框架注册的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="dfa31-432">下表列出了框架注册的服务的一小部分。</span><span class="sxs-lookup"><span data-stu-id="dfa31-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="dfa31-433">服务类型</span><span class="sxs-lookup"><span data-stu-id="dfa31-433">Service Type</span></span> | <span data-ttu-id="dfa31-434">生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="dfa31-435">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="dfa31-436">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="dfa31-437">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="dfa31-438">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="dfa31-439">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="dfa31-440">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="dfa31-441">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="dfa31-442">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="dfa31-443">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="dfa31-444">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="dfa31-445">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="dfa31-446">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="dfa31-447">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="dfa31-448">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="dfa31-449">其他资源</span><span class="sxs-lookup"><span data-stu-id="dfa31-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="dfa31-450">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="dfa31-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="dfa31-451">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="dfa31-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* <span data-ttu-id="dfa31-452">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="dfa31-452">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="dfa31-453">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="dfa31-453">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="dfa31-454">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-454">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dfa31-455">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="dfa31-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="dfa31-456">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="dfa31-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="dfa31-457">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="dfa31-458">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="dfa31-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="dfa31-459">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="dfa31-459">Overview of dependency injection</span></span>

<span data-ttu-id="dfa31-460">依赖项是指另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="dfa31-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="dfa31-461">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="dfa31-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="dfa31-462">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="dfa31-463">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="dfa31-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="dfa31-464">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="dfa31-465">代码依赖关系（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="dfa31-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="dfa31-466">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="dfa31-467">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="dfa31-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="dfa31-468">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="dfa31-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="dfa31-469">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="dfa31-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="dfa31-470">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="dfa31-471">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="dfa31-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="dfa31-472">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="dfa31-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="dfa31-473">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="dfa31-474">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="dfa31-475">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="dfa31-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="dfa31-476">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="dfa31-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="dfa31-477">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="dfa31-478">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="dfa31-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="dfa31-479">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="dfa31-480">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="dfa31-481">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="dfa31-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="dfa31-482">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="dfa31-483">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="dfa31-484">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="dfa31-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="dfa31-485">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="dfa31-486">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="dfa31-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dfa31-487">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="dfa31-488">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="dfa31-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="dfa31-489">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="dfa31-490">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="dfa31-491">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="dfa31-492">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="dfa31-493">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="dfa31-494">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="dfa31-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="dfa31-495">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="dfa31-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="dfa31-496">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="dfa31-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="dfa31-497">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="dfa31-498">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="dfa31-499">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="dfa31-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="dfa31-500">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-500">Services injected into Startup</span></span>

<span data-ttu-id="dfa31-501">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="dfa31-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="dfa31-502">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="dfa31-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="dfa31-503">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="dfa31-504">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-504">Framework-provided services</span></span>

<span data-ttu-id="dfa31-505">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="dfa31-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="dfa31-506">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="dfa31-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="dfa31-507">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="dfa31-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="dfa31-508">下表列出了框架注册的服务的一小部分。</span><span class="sxs-lookup"><span data-stu-id="dfa31-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="dfa31-509">服务类型</span><span class="sxs-lookup"><span data-stu-id="dfa31-509">Service Type</span></span> | <span data-ttu-id="dfa31-510">生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="dfa31-511">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="dfa31-512">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="dfa31-513">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="dfa31-514">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="dfa31-515">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="dfa31-516">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="dfa31-517">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="dfa31-518">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="dfa31-519">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="dfa31-520">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="dfa31-521">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="dfa31-522">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="dfa31-523">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="dfa31-524">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="dfa31-525">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-525">Register additional services with extension methods</span></span>

<span data-ttu-id="dfa31-526">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="dfa31-527">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="dfa31-528">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="dfa31-529">服务生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-529">Service lifetimes</span></span>

<span data-ttu-id="dfa31-530">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="dfa31-531">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="dfa31-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="dfa31-532">暂时</span><span class="sxs-lookup"><span data-stu-id="dfa31-532">Transient</span></span>

<span data-ttu-id="dfa31-533">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="dfa31-534">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="dfa31-535">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="dfa31-536">作用域</span><span class="sxs-lookup"><span data-stu-id="dfa31-536">Scoped</span></span>

<span data-ttu-id="dfa31-537">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="dfa31-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="dfa31-538">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="dfa31-539">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="dfa31-540">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="dfa31-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="dfa31-541">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="dfa31-542">单例</span><span class="sxs-lookup"><span data-stu-id="dfa31-542">Singleton</span></span>

<span data-ttu-id="dfa31-543">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="dfa31-544">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="dfa31-545">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="dfa31-546">不要在类中实现单一实例设计模式并提供用户代码来管理对象的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="dfa31-547">在处理请求的应用中，在应用关闭，释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="dfa31-548">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="dfa31-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="dfa31-549">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="dfa31-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="dfa31-550">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="dfa31-550">Service registration methods</span></span>

<span data-ttu-id="dfa31-551">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="dfa31-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="dfa31-552">方法</span><span class="sxs-lookup"><span data-stu-id="dfa31-552">Method</span></span> | <span data-ttu-id="dfa31-553">自动</span><span class="sxs-lookup"><span data-stu-id="dfa31-553">Automatic</span></span><br><span data-ttu-id="dfa31-554">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="dfa31-554">object</span></span><br><span data-ttu-id="dfa31-555">处置</span><span class="sxs-lookup"><span data-stu-id="dfa31-555">disposal</span></span> | <span data-ttu-id="dfa31-556">多种</span><span class="sxs-lookup"><span data-stu-id="dfa31-556">Multiple</span></span><br><span data-ttu-id="dfa31-557">实现</span><span class="sxs-lookup"><span data-stu-id="dfa31-557">implementations</span></span> | <span data-ttu-id="dfa31-558">传递参数</span><span class="sxs-lookup"><span data-stu-id="dfa31-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="dfa31-559">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="dfa31-560">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-560">Yes</span></span> | <span data-ttu-id="dfa31-561">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-561">Yes</span></span> | <span data-ttu-id="dfa31-562">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="dfa31-563">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="dfa31-564">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-564">Yes</span></span> | <span data-ttu-id="dfa31-565">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-565">Yes</span></span> | <span data-ttu-id="dfa31-566">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="dfa31-567">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="dfa31-568">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-568">Yes</span></span> | <span data-ttu-id="dfa31-569">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-569">No</span></span> | <span data-ttu-id="dfa31-570">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="dfa31-571">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="dfa31-572">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-572">No</span></span> | <span data-ttu-id="dfa31-573">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-573">Yes</span></span> | <span data-ttu-id="dfa31-574">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="dfa31-575">示例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="dfa31-576">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-576">No</span></span> | <span data-ttu-id="dfa31-577">否</span><span class="sxs-lookup"><span data-stu-id="dfa31-577">No</span></span> | <span data-ttu-id="dfa31-578">是</span><span class="sxs-lookup"><span data-stu-id="dfa31-578">Yes</span></span> |

<span data-ttu-id="dfa31-579">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="dfa31-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="dfa31-580">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="dfa31-581">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="dfa31-582">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="dfa31-583">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="dfa31-584">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="dfa31-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="dfa31-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="dfa31-586">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="dfa31-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="dfa31-587">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="dfa31-588">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="dfa31-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="dfa31-589">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="dfa31-590">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="dfa31-591">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="dfa31-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="dfa31-592">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="dfa31-592">Constructor injection behavior</span></span>

<span data-ttu-id="dfa31-593">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="dfa31-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="dfa31-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="dfa31-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="dfa31-595">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="dfa31-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="dfa31-596">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="dfa31-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="dfa31-597">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="dfa31-598">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="dfa31-599">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="dfa31-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="dfa31-600">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="dfa31-600">Entity Framework contexts</span></span>

<span data-ttu-id="dfa31-601">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="dfa31-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="dfa31-602">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="dfa31-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="dfa31-603">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="dfa31-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="dfa31-604">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="dfa31-604">Lifetime and registration options</span></span>

<span data-ttu-id="dfa31-605">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="dfa31-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="dfa31-606">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="dfa31-607">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="dfa31-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="dfa31-608">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="dfa31-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="dfa31-609">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="dfa31-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="dfa31-610">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="dfa31-611">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="dfa31-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="dfa31-612">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="dfa31-613">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="dfa31-614">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="dfa31-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="dfa31-615">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="dfa31-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="dfa31-616">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="dfa31-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="dfa31-617">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="dfa31-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="dfa31-618">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="dfa31-619">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="dfa31-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="dfa31-620">示例应用演示了各个请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="dfa31-621">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="dfa31-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="dfa31-622">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="dfa31-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="dfa31-623">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="dfa31-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="dfa31-624">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="dfa31-624">**First request:**</span></span>

<span data-ttu-id="dfa31-625">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="dfa31-625">Controller operations:</span></span>

<span data-ttu-id="dfa31-626">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="dfa31-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="dfa31-627">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="dfa31-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="dfa31-628">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="dfa31-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="dfa31-629">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="dfa31-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="dfa31-630">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="dfa31-630">`OperationService` operations:</span></span>

<span data-ttu-id="dfa31-631">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="dfa31-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="dfa31-632">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="dfa31-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="dfa31-633">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="dfa31-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="dfa31-634">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="dfa31-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="dfa31-635">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="dfa31-635">**Second request:**</span></span>

<span data-ttu-id="dfa31-636">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="dfa31-636">Controller operations:</span></span>

<span data-ttu-id="dfa31-637">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="dfa31-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="dfa31-638">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="dfa31-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="dfa31-639">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="dfa31-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="dfa31-640">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="dfa31-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="dfa31-641">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="dfa31-641">`OperationService` operations:</span></span>

<span data-ttu-id="dfa31-642">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="dfa31-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="dfa31-643">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="dfa31-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="dfa31-644">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="dfa31-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="dfa31-645">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="dfa31-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="dfa31-646">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="dfa31-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="dfa31-647">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="dfa31-647">*Transient* objects are always different.</span></span> <span data-ttu-id="dfa31-648">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="dfa31-649">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="dfa31-650">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="dfa31-651">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="dfa31-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="dfa31-652">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-652">Call services from main</span></span>

<span data-ttu-id="dfa31-653">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="dfa31-654">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="dfa31-655">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="dfa31-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="dfa31-656">作用域验证</span><span class="sxs-lookup"><span data-stu-id="dfa31-656">Scope validation</span></span>

<span data-ttu-id="dfa31-657">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="dfa31-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="dfa31-658">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="dfa31-659">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="dfa31-660">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="dfa31-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="dfa31-661">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="dfa31-662">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="dfa31-663">如果作用域服务创建于根容器，则该服务的生存期会实际上提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="dfa31-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="dfa31-664">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="dfa31-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="dfa31-665">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="dfa31-666">请求服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-666">Request Services</span></span>

<span data-ttu-id="dfa31-667">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="dfa31-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="dfa31-668">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="dfa31-669">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="dfa31-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="dfa31-670">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="dfa31-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="dfa31-671">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="dfa31-672">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="dfa31-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="dfa31-673">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="dfa31-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="dfa31-674">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-674">Design services for dependency injection</span></span>

<span data-ttu-id="dfa31-675">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="dfa31-675">Best practices are to:</span></span>

* <span data-ttu-id="dfa31-676">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="dfa31-677">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="dfa31-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="dfa31-678">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="dfa31-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="dfa31-679">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="dfa31-680">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="dfa31-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="dfa31-681">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="dfa31-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="dfa31-682">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="dfa31-683">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="dfa31-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="dfa31-684">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="dfa31-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="dfa31-685">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="dfa31-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="dfa31-686">服务释放</span><span class="sxs-lookup"><span data-stu-id="dfa31-686">Disposal of services</span></span>

<span data-ttu-id="dfa31-687">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="dfa31-688">如果实例是通过用户代码添加到容器中，则不会自动释放该实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="dfa31-689">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="dfa31-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="dfa31-690">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="dfa31-690">In the following example:</span></span>

* <span data-ttu-id="dfa31-691">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="dfa31-692">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="dfa31-693">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="dfa31-694">服务如果未通过开发者代码显式释放，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="dfa31-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="dfa31-695">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="dfa31-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="dfa31-696">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-696">Transient, limited lifetime</span></span>

<span data-ttu-id="dfa31-697">**方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-697">**Scenario**</span></span>

<span data-ttu-id="dfa31-698">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="dfa31-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="dfa31-699">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="dfa31-700">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="dfa31-701">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-701">**Solution**</span></span>

<span data-ttu-id="dfa31-702">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="dfa31-703">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="dfa31-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="dfa31-704">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="dfa31-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="dfa31-705">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="dfa31-706">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="dfa31-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="dfa31-707">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="dfa31-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="dfa31-708">**方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-708">**Scenario**</span></span>

<span data-ttu-id="dfa31-709">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="dfa31-710">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="dfa31-710">**Solution**</span></span>

<span data-ttu-id="dfa31-711">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="dfa31-712">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="dfa31-713">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="dfa31-714">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="dfa31-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="dfa31-715">通用准则</span><span class="sxs-lookup"><span data-stu-id="dfa31-715">General Guidelines</span></span>

* <span data-ttu-id="dfa31-716">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="dfa31-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="dfa31-717">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="dfa31-718">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="dfa31-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="dfa31-719">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="dfa31-720">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="dfa31-721"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="dfa31-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="dfa31-722">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="dfa31-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="dfa31-723">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="dfa31-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="dfa31-724">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="dfa31-724">Default service container replacement</span></span>

<span data-ttu-id="dfa31-725">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="dfa31-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="dfa31-726">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="dfa31-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="dfa31-727">属性注入</span><span class="sxs-lookup"><span data-stu-id="dfa31-727">Property injection</span></span>
* <span data-ttu-id="dfa31-728">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="dfa31-728">Injection based on name</span></span>
* <span data-ttu-id="dfa31-729">子容器</span><span class="sxs-lookup"><span data-stu-id="dfa31-729">Child containers</span></span>
* <span data-ttu-id="dfa31-730">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="dfa31-730">Custom lifetime management</span></span>
* <span data-ttu-id="dfa31-731">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="dfa31-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="dfa31-732">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="dfa31-732">Convention-based registration</span></span>

<span data-ttu-id="dfa31-733">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="dfa31-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="dfa31-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="dfa31-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="dfa31-735">DryIoc</span><span class="sxs-lookup"><span data-stu-id="dfa31-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="dfa31-736">Grace</span><span class="sxs-lookup"><span data-stu-id="dfa31-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="dfa31-737">LightInject</span><span class="sxs-lookup"><span data-stu-id="dfa31-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="dfa31-738">Lamar</span><span class="sxs-lookup"><span data-stu-id="dfa31-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="dfa31-739">Stashbox</span><span class="sxs-lookup"><span data-stu-id="dfa31-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="dfa31-740">Unity</span><span class="sxs-lookup"><span data-stu-id="dfa31-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="dfa31-741">线程安全</span><span class="sxs-lookup"><span data-stu-id="dfa31-741">Thread safety</span></span>

<span data-ttu-id="dfa31-742">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="dfa31-743">如果单例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="dfa31-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="dfa31-744">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="dfa31-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="dfa31-745">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="dfa31-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="dfa31-746">建议</span><span class="sxs-lookup"><span data-stu-id="dfa31-746">Recommendations</span></span>

* <span data-ttu-id="dfa31-747">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="dfa31-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="dfa31-748">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="dfa31-749">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="dfa31-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="dfa31-750">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="dfa31-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="dfa31-751">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="dfa31-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="dfa31-752">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="dfa31-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="dfa31-753">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="dfa31-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="dfa31-754">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="dfa31-754">Avoid static access to services.</span></span> <span data-ttu-id="dfa31-755">例如，避免静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="dfa31-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="dfa31-756">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="dfa31-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="dfa31-757">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="dfa31-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="dfa31-758">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="dfa31-758">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="dfa31-759">**正确**：</span><span class="sxs-lookup"><span data-stu-id="dfa31-759">**Correct**:</span></span>

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

* <span data-ttu-id="dfa31-760">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="dfa31-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="dfa31-761">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="dfa31-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="dfa31-762">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="dfa31-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="dfa31-763">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="dfa31-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="dfa31-764">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="dfa31-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="dfa31-765">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="dfa31-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dfa31-766">其他资源</span><span class="sxs-lookup"><span data-stu-id="dfa31-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="dfa31-767">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="dfa31-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="dfa31-768">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="dfa31-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* <span data-ttu-id="dfa31-769">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="dfa31-769">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="dfa31-770">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="dfa31-770">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="dfa31-771">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="dfa31-771">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
