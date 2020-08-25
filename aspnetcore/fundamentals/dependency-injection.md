---
title: ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628789"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="5f951-103">ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="5f951-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5f951-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="5f951-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="5f951-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="5f951-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="5f951-106">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="5f951-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="5f951-107">有关选项的依赖项注入的详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="5f951-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5f951-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5f951-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="5f951-109">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="5f951-109">Overview of dependency injection</span></span>

<span data-ttu-id="5f951-110">依赖项是指另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="5f951-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="5f951-111">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="5f951-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="5f951-112">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="5f951-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="5f951-113">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="5f951-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="5f951-114">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="5f951-115">代码依赖项（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="5f951-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="5f951-116">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="5f951-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="5f951-117">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="5f951-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="5f951-118">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="5f951-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="5f951-119">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="5f951-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="5f951-120">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="5f951-121">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="5f951-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="5f951-122">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="5f951-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="5f951-123">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="5f951-124">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="5f951-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="5f951-125">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="5f951-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="5f951-126">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="5f951-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="5f951-127">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="5f951-128">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="5f951-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="5f951-129">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="5f951-130">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="5f951-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 方法使用范围内生存期（单个请求的生存期）注册服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="5f951-132">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="5f951-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="5f951-133">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="5f951-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="5f951-134">使用 DI 模式：</span><span class="sxs-lookup"><span data-stu-id="5f951-134">With the DI pattern:</span></span>

* <span data-ttu-id="5f951-135">控制器不使用具体类型 `MyDependency`，仅使用 `IMyDependency` 接口。</span><span class="sxs-lookup"><span data-stu-id="5f951-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="5f951-136">这样可以轻松地更改控制器使用的实现，而无需修改控制器。</span><span class="sxs-lookup"><span data-stu-id="5f951-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="5f951-137">控制器不创建 `MyDependency` 的实例，它由 DI 容器创建。</span><span class="sxs-lookup"><span data-stu-id="5f951-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="5f951-138">可以通过使用内置日志记录 API 来改善 `IMyDependency` 接口的实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="5f951-139">更新的 `ConfigureServices` 方法注册新的 `IMyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="5f951-140">`MyDependency2` 请求构造函数中的 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="5f951-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="5f951-141">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="5f951-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="5f951-142">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="5f951-143">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="5f951-144">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="5f951-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="5f951-145">`ILogger<TCategoryName>` 是[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="5f951-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="5f951-146">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="5f951-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="5f951-147">在依赖项注入术语中，服务：</span><span class="sxs-lookup"><span data-stu-id="5f951-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="5f951-148">通常是向应用中其他代码提供服务的对象，如 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="5f951-149">与 Web 服务无关，尽管服务可能使用 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="5f951-150">框架提供可靠的[日志记录](xref:fundamentals/logging/index)系统。</span><span class="sxs-lookup"><span data-stu-id="5f951-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="5f951-151">编写 `IMyDependency` 实现来演示基本的 DI，而不是来实现日志记录。</span><span class="sxs-lookup"><span data-stu-id="5f951-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="5f951-152">大多数应用都不需要编写记录器。</span><span class="sxs-lookup"><span data-stu-id="5f951-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="5f951-153">下面的代码演示如何使用默认日志记录，这不要求在 `ConfigureServices` 中注册任何服务：</span><span class="sxs-lookup"><span data-stu-id="5f951-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="5f951-154">使用前面的代码时，无需更新 `ConfigureServices`，因为框架提供[日志记录](xref:fundamentals/logging/index)：</span><span class="sxs-lookup"><span data-stu-id="5f951-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="5f951-155">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-155">Services injected into Startup</span></span>

<span data-ttu-id="5f951-156">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="5f951-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="5f951-157">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="5f951-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="5f951-158">有关详细信息，请参阅 <xref:fundamentals/startup> 和[访问 Startup 中的配置](xref:fundamentals/configuration/index#access-configuration-in-startup)。</span><span class="sxs-lookup"><span data-stu-id="5f951-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="5f951-159">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="5f951-159">Register additional services with extension methods</span></span>

<span data-ttu-id="5f951-160">当服务集合扩展方法可用于注册服务时：</span><span class="sxs-lookup"><span data-stu-id="5f951-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="5f951-161">约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="5f951-162">也会注册从属服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-162">The dependent services are also registered.</span></span>

<span data-ttu-id="5f951-163">下面的代码通过个人用户帐户由 Razor 页面模板生成，并演示如何使用扩展方法 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> 将其他服务添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="5f951-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="5f951-164">有关详细信息，请参阅 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 和 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="5f951-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="5f951-165">有关编写用于注册服务的扩展方法的说明，请参阅[合并服务集合](#csc)部分。</span><span class="sxs-lookup"><span data-stu-id="5f951-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="5f951-166">服务生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-166">Service lifetimes</span></span>

<span data-ttu-id="5f951-167">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="5f951-168">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="5f951-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="5f951-169">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-169">Transient</span></span>

<span data-ttu-id="5f951-170">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="5f951-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="5f951-171">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="5f951-172">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="5f951-173">作用域</span><span class="sxs-lookup"><span data-stu-id="5f951-173">Scoped</span></span>

<span data-ttu-id="5f951-174">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="5f951-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="5f951-175">使用 Entity Framework Core 时，默认情况下 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展方法使用范围内生存期来注册 `DbContext` 类型。</span><span class="sxs-lookup"><span data-stu-id="5f951-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="5f951-176">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="5f951-177">通过以下方法之一在中间件中使用范围内服务：</span><span class="sxs-lookup"><span data-stu-id="5f951-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="5f951-178">将服务注入 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="5f951-179">通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入会在运行时引发异常，因为它强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="5f951-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="5f951-180">[生存期和注册选项](#lifetime-and-registration-options)中的示例使用 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="5f951-181">[基于工厂的中间件](<xref:fundamentals/middleware/extensibility>)。</span><span class="sxs-lookup"><span data-stu-id="5f951-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="5f951-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 扩展方法检查中间件的已注册类型是否实现 <xref:Microsoft.AspNetCore.Http.IMiddleware>。</span><span class="sxs-lookup"><span data-stu-id="5f951-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="5f951-183">如果是，则使用在容器中注册的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实例来解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 实现，而不使用基于约定的中间件激活逻辑。</span><span class="sxs-lookup"><span data-stu-id="5f951-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="5f951-184">中间件在应用的服务容器中注册为作用域或瞬态服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="5f951-185">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="5f951-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="5f951-186">不要从单一实例解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="5f951-187">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="5f951-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="5f951-188">可以：</span><span class="sxs-lookup"><span data-stu-id="5f951-188">It's fine to:</span></span>

* <span data-ttu-id="5f951-189">从范围内或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="5f951-190">从其他范围内或暂时性服务解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="5f951-191">默认情况下在开发环境中，从具有较长生存期的其他服务解析服务将引发异常。</span><span class="sxs-lookup"><span data-stu-id="5f951-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="5f951-192">有关详细信息，请参阅[作用域验证](#sv)。</span><span class="sxs-lookup"><span data-stu-id="5f951-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="5f951-193">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-193">Singleton</span></span>

<span data-ttu-id="5f951-194">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)：</span><span class="sxs-lookup"><span data-stu-id="5f951-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="5f951-195">在首次请求它们时进行创建；或者</span><span class="sxs-lookup"><span data-stu-id="5f951-195">The first time they're requested.</span></span>
* <span data-ttu-id="5f951-196">在向容器直接提供实现实例时由开发人员进行创建。</span><span class="sxs-lookup"><span data-stu-id="5f951-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="5f951-197">很少用到此方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-197">This approach is rarely needed.</span></span>

<span data-ttu-id="5f951-198">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="5f951-199">如果应用需要单一实例行为，则允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="5f951-200">不要实现单一实例设计模式，或提供代码来释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="5f951-201">服务永远不应由解析容器服务的代码释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="5f951-202">如果类型或工厂注册为单一实例，则容器将自动释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="5f951-203">单一实例服务必须是线程安全的，并且通常在无状态服务中使用。</span><span class="sxs-lookup"><span data-stu-id="5f951-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="5f951-204">在处理请求的应用中，如果在应用程序关闭时释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="5f951-205">由于应用关闭之前不释放内存，因此必须考虑单一实例的内存使用。</span><span class="sxs-lookup"><span data-stu-id="5f951-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="5f951-206">不要从单一实例解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="5f951-207">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="5f951-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="5f951-208">可以从范围内或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="5f951-209">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="5f951-209">Service registration methods</span></span>

<span data-ttu-id="5f951-210">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="5f951-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="5f951-211">方法</span><span class="sxs-lookup"><span data-stu-id="5f951-211">Method</span></span> | <span data-ttu-id="5f951-212">自动</span><span class="sxs-lookup"><span data-stu-id="5f951-212">Automatic</span></span><br><span data-ttu-id="5f951-213">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="5f951-213">object</span></span><br><span data-ttu-id="5f951-214">处置</span><span class="sxs-lookup"><span data-stu-id="5f951-214">disposal</span></span> | <span data-ttu-id="5f951-215">多种</span><span class="sxs-lookup"><span data-stu-id="5f951-215">Multiple</span></span><br><span data-ttu-id="5f951-216">实现</span><span class="sxs-lookup"><span data-stu-id="5f951-216">implementations</span></span> | <span data-ttu-id="5f951-217">传递参数</span><span class="sxs-lookup"><span data-stu-id="5f951-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="5f951-218">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="5f951-219">是</span><span class="sxs-lookup"><span data-stu-id="5f951-219">Yes</span></span> | <span data-ttu-id="5f951-220">是</span><span class="sxs-lookup"><span data-stu-id="5f951-220">Yes</span></span> | <span data-ttu-id="5f951-221">否</span><span class="sxs-lookup"><span data-stu-id="5f951-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="5f951-222">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="5f951-223">“是”</span><span class="sxs-lookup"><span data-stu-id="5f951-223">Yes</span></span> | <span data-ttu-id="5f951-224">是</span><span class="sxs-lookup"><span data-stu-id="5f951-224">Yes</span></span> | <span data-ttu-id="5f951-225">是</span><span class="sxs-lookup"><span data-stu-id="5f951-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="5f951-226">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="5f951-227">是</span><span class="sxs-lookup"><span data-stu-id="5f951-227">Yes</span></span> | <span data-ttu-id="5f951-228">否</span><span class="sxs-lookup"><span data-stu-id="5f951-228">No</span></span> | <span data-ttu-id="5f951-229">否</span><span class="sxs-lookup"><span data-stu-id="5f951-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="5f951-230">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="5f951-231">否</span><span class="sxs-lookup"><span data-stu-id="5f951-231">No</span></span> | <span data-ttu-id="5f951-232">是</span><span class="sxs-lookup"><span data-stu-id="5f951-232">Yes</span></span> | <span data-ttu-id="5f951-233">是</span><span class="sxs-lookup"><span data-stu-id="5f951-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="5f951-234">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="5f951-235">否</span><span class="sxs-lookup"><span data-stu-id="5f951-235">No</span></span> | <span data-ttu-id="5f951-236">否</span><span class="sxs-lookup"><span data-stu-id="5f951-236">No</span></span> | <span data-ttu-id="5f951-237">是</span><span class="sxs-lookup"><span data-stu-id="5f951-237">Yes</span></span> |

<span data-ttu-id="5f951-238">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="5f951-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="5f951-239">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="5f951-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="5f951-240">`TryAdd{LIFETIME}` 方法在尚未注册实现情况下注册该服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="5f951-241">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="5f951-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="5f951-242">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="5f951-243">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="5f951-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="5f951-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法在没有同一类型实现的情况下注册该服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="5f951-245">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="5f951-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="5f951-246">注册服务时，开发人员应在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="5f951-247">通常库作者使用 `TryAddEnumerable` 来避免在容器中注册实现的多个副本。</span><span class="sxs-lookup"><span data-stu-id="5f951-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="5f951-248">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="5f951-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="5f951-249">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="5f951-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="5f951-250">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="5f951-251">服务注册通常与顺序无关，除了注册同一类型的多个实现时。</span><span class="sxs-lookup"><span data-stu-id="5f951-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="5f951-252">`IServiceCollection` 是 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 的集合。</span><span class="sxs-lookup"><span data-stu-id="5f951-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="5f951-253">下面的代码演示如何使用构造函数添加服务：</span><span class="sxs-lookup"><span data-stu-id="5f951-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="5f951-254">`Add{LIFETIME}` 方法使用相同的方式。</span><span class="sxs-lookup"><span data-stu-id="5f951-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="5f951-255">相关示例请参阅 [AddScoped 的源代码](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)。</span><span class="sxs-lookup"><span data-stu-id="5f951-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="5f951-256">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="5f951-256">Constructor injection behavior</span></span>

<span data-ttu-id="5f951-257">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="5f951-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="5f951-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="5f951-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="5f951-259">在依赖项注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="5f951-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="5f951-260">与框架功能一起使用，例如[标记帮助程序](xref:mvc/views/tag-helpers/intro)、[MVC 控制器](xref:mvc/models/model-binding)和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="5f951-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="5f951-261">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="5f951-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="5f951-262">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="5f951-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="5f951-263">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="5f951-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="5f951-264">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="5f951-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="5f951-265">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="5f951-265">Entity Framework contexts</span></span>

<span data-ttu-id="5f951-266">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="5f951-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="5f951-267">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="5f951-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="5f951-268">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="5f951-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="5f951-269">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="5f951-269">Lifetime and registration options</span></span>

<span data-ttu-id="5f951-270">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="5f951-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="5f951-271">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="5f951-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="5f951-272">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="5f951-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="5f951-273">`Operation` 构造函数生成 GUID 的最后 4 个字符（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="5f951-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="5f951-274">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="5f951-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="5f951-275">示例应用演示了请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="5f951-276">示例应用的 `IndexModel` 和中间件请求每种 `IOperation` 类型并记录 `OperationId`：</span><span class="sxs-lookup"><span data-stu-id="5f951-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="5f951-277">中间件与 `IndexModel` 类似，并解析相同的服务：</span><span class="sxs-lookup"><span data-stu-id="5f951-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="5f951-278">范围内服务必须在 `InvokeAsync` 方法中进行解析：</span><span class="sxs-lookup"><span data-stu-id="5f951-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="5f951-279">记录器输出显示：</span><span class="sxs-lookup"><span data-stu-id="5f951-279">The logger output shows:</span></span>

* <span data-ttu-id="5f951-280">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="5f951-280">*Transient* objects are always different.</span></span> <span data-ttu-id="5f951-281">`IndexModel` 和中间件中的临时 `OperationId` 值不同。</span><span class="sxs-lookup"><span data-stu-id="5f951-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="5f951-282">范围内对象在每个请求中是相同的，但在请求之间不同。</span><span class="sxs-lookup"><span data-stu-id="5f951-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="5f951-283">单一实例对象对于每个请求是相同的。</span><span class="sxs-lookup"><span data-stu-id="5f951-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="5f951-284">若要减少日志记录输出，请在 appsettings.Development.json 文件中设置“Logging:LogLevel:Microsoft:Error”：</span><span class="sxs-lookup"><span data-stu-id="5f951-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="5f951-285">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="5f951-285">Call services from main</span></span>

<span data-ttu-id="5f951-286">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="5f951-287">此方法可用于在启动时访问范围内服务以运行初始化任务：</span><span class="sxs-lookup"><span data-stu-id="5f951-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

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

<span data-ttu-id="5f951-288">前面的代码源自 Razor 页面教程的[添加种子初始值设定项](xref:tutorials/razor-pages/sql?#add-the-seed-initializer)。</span><span class="sxs-lookup"><span data-stu-id="5f951-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="5f951-289">以下示例演示如何在 `Program.Main` 中获取 `IMyDependency` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="5f951-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="5f951-290">作用域验证</span><span class="sxs-lookup"><span data-stu-id="5f951-290">Scope validation</span></span>

<span data-ttu-id="5f951-291">如果应用正在[开发环境](xref:fundamentals/environments)中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 以生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="5f951-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="5f951-292">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="5f951-293">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="5f951-294">未将暂时性服务直接或间接注入单一实例或范围内服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="5f951-295">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="5f951-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="5f951-296">在启动提供程序和应用时，根服务提供程序的生存期对应于应用的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="5f951-297">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="5f951-298">如果范围内服务创建于根容器，则该服务的生存期实际上提升至单一实例，因为根容器只会在应用关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="5f951-299">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="5f951-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="5f951-300">有关详细信息，请参阅[作用域验证](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="5f951-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="5f951-301">请求服务</span><span class="sxs-lookup"><span data-stu-id="5f951-301">Request Services</span></span>

<span data-ttu-id="5f951-302">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="5f951-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="5f951-303">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="5f951-304">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="5f951-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="5f951-305">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="5f951-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="5f951-306">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="5f951-307">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="5f951-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="5f951-308">ASP.NET Core 为每个请求创建一个范围，`RequestServices` 公开范围内服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="5f951-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="5f951-309">只要请求处于活动状态，所有范围内的服务都有效。</span><span class="sxs-lookup"><span data-stu-id="5f951-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="5f951-310">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="5f951-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="5f951-311">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-311">Design services for dependency injection</span></span>

<span data-ttu-id="5f951-312">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="5f951-312">Best practices are to:</span></span>

* <span data-ttu-id="5f951-313">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="5f951-314">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="5f951-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="5f951-315">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="5f951-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="5f951-316">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="5f951-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="5f951-317">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="5f951-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="5f951-318">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="5f951-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="5f951-319">如果一个类似乎有过多的注入依赖项，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="5f951-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="5f951-320">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="5f951-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="5f951-321">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="5f951-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="5f951-322">服务释放</span><span class="sxs-lookup"><span data-stu-id="5f951-322">Disposal of services</span></span>

<span data-ttu-id="5f951-323">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="5f951-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="5f951-324">服务永远不应由任何解析容器服务的代码释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="5f951-325">如果类型或工厂注册为单一实例，则容器释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="5f951-326">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="5f951-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5f951-327">每次刷新索引页后，调试控制台显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="5f951-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="5f951-328">不由服务容器创建的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="5f951-329">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="5f951-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="5f951-330">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="5f951-330">In the preceding code:</span></span>

* <span data-ttu-id="5f951-331">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="5f951-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="5f951-332">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="5f951-333">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="5f951-334">服务如果未通过开发者代码显式释放，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5f951-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="5f951-335">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="5f951-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="5f951-336">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-336">Transient, limited lifetime</span></span>

<span data-ttu-id="5f951-337">**方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-337">**Scenario**</span></span>

<span data-ttu-id="5f951-338">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="5f951-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="5f951-339">在根范围（根容器）内解析实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="5f951-340">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="5f951-341">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-341">**Solution**</span></span>

<span data-ttu-id="5f951-342">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="5f951-343">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="5f951-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="5f951-344">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="5f951-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="5f951-345">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="5f951-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="5f951-346">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="5f951-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="5f951-347">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="5f951-348">**方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-348">**Scenario**</span></span>

<span data-ttu-id="5f951-349">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="5f951-350">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-350">**Solution**</span></span>

<span data-ttu-id="5f951-351">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="5f951-352">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="5f951-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="5f951-353">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="5f951-354">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="5f951-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="5f951-355">一般 IDisposable 准则</span><span class="sxs-lookup"><span data-stu-id="5f951-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="5f951-356">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="5f951-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="5f951-357">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="5f951-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="5f951-358">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="5f951-359">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="5f951-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="5f951-360">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="5f951-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="5f951-361"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="5f951-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="5f951-362">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="5f951-363">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="5f951-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="5f951-364">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="5f951-364">Default service container replacement</span></span>

<span data-ttu-id="5f951-365">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="5f951-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="5f951-366">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="5f951-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="5f951-367">属性注入</span><span class="sxs-lookup"><span data-stu-id="5f951-367">Property injection</span></span>
* <span data-ttu-id="5f951-368">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="5f951-368">Injection based on name</span></span>
* <span data-ttu-id="5f951-369">子容器</span><span class="sxs-lookup"><span data-stu-id="5f951-369">Child containers</span></span>
* <span data-ttu-id="5f951-370">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="5f951-370">Custom lifetime management</span></span>
* <span data-ttu-id="5f951-371">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="5f951-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="5f951-372">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="5f951-372">Convention-based registration</span></span>

<span data-ttu-id="5f951-373">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="5f951-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="5f951-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="5f951-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="5f951-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="5f951-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="5f951-376">Grace</span><span class="sxs-lookup"><span data-stu-id="5f951-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="5f951-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="5f951-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="5f951-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="5f951-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="5f951-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="5f951-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="5f951-380">Unity</span><span class="sxs-lookup"><span data-stu-id="5f951-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="5f951-381">线程安全</span><span class="sxs-lookup"><span data-stu-id="5f951-381">Thread safety</span></span>

<span data-ttu-id="5f951-382">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="5f951-383">如果单例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="5f951-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="5f951-384">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="5f951-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="5f951-385">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="5f951-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="5f951-386">建议</span><span class="sxs-lookup"><span data-stu-id="5f951-386">Recommendations</span></span>

* <span data-ttu-id="5f951-387">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="5f951-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="5f951-388">C# 不支持异步构造函数。</span><span class="sxs-lookup"><span data-stu-id="5f951-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="5f951-389">建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="5f951-390">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="5f951-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="5f951-391">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="5f951-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="5f951-392">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="5f951-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="5f951-393">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="5f951-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="5f951-394">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="5f951-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="5f951-395">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-395">Avoid static access to services.</span></span> <span data-ttu-id="5f951-396">例如，避免静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="5f951-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="5f951-397">使 DI 工厂保持快速且同步。</span><span class="sxs-lookup"><span data-stu-id="5f951-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="5f951-398">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="5f951-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="5f951-399">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="5f951-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="5f951-400">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="5f951-400">**Incorrect:**</span></span>

    ![错误代码](dependency-injection/_static/bad.png)

  <span data-ttu-id="5f951-402">**正确**：</span><span class="sxs-lookup"><span data-stu-id="5f951-402">**Correct**:</span></span>

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
* <span data-ttu-id="5f951-403">要避免的另一个服务定位器变体是注入需在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="5f951-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="5f951-404">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="5f951-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="5f951-405">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="5f951-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="5f951-406">避免在 `ConfigureServices` 中调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>。</span><span class="sxs-lookup"><span data-stu-id="5f951-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="5f951-407">当开发人员想要在 `ConfigureServices` 中解析服务时，通常会调用 `BuildServiceProvider`。</span><span class="sxs-lookup"><span data-stu-id="5f951-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="5f951-408">例如，假设需要从配置中获取 `LoginPath`。</span><span class="sxs-lookup"><span data-stu-id="5f951-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="5f951-409">避免以下代码：</span><span class="sxs-lookup"><span data-stu-id="5f951-409">Avoid the following code:</span></span>

  ![调用 BuildServiceProvider 的错误代码](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="5f951-411">在上图中，选择 `services.BuildServiceProvider` 下的绿色波浪线将显示以下 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="5f951-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="5f951-412">ASP0000 从应用程序代码调用“BuildServiceProvider”会导致创建单一实例服务的其他副本。</span><span class="sxs-lookup"><span data-stu-id="5f951-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="5f951-413">考虑依赖项注入服务等替代项作为“Configure”的参数。</span><span class="sxs-lookup"><span data-stu-id="5f951-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="5f951-414">调用 `BuildServiceProvider` 会创建第二个容器，该容器可创建残缺的单一实例并导致跨多个容器引用对象图。</span><span class="sxs-lookup"><span data-stu-id="5f951-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="5f951-415">获取 `LoginPath` 的正确方法是将选项模式用于 DI：</span><span class="sxs-lookup"><span data-stu-id="5f951-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="5f951-416">可释放的暂时性服务由容器捕获以进行释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="5f951-417">如果从顶级容器解析，这会变为内存泄漏。</span><span class="sxs-lookup"><span data-stu-id="5f951-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="5f951-418">启用范围验证，确保应用不具有捕获单一实例的范围内服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="5f951-419">有关详细信息，请参阅[作用域验证](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="5f951-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="5f951-420">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="5f951-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="5f951-421">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="5f951-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="5f951-422">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="5f951-423">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="5f951-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="5f951-424">DI 中适用于多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="5f951-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="5f951-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="5f951-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="5f951-426">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="5f951-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="5f951-427">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="5f951-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5f951-428">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-428">Framework-provided services</span></span>

<span data-ttu-id="5f951-429">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="5f951-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="5f951-430">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="5f951-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="5f951-431">基于 ASP.NET Core 模板的应用包含 250 个以上由框架注册的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="5f951-432">下表列出了框架注册的服务的一小部分。</span><span class="sxs-lookup"><span data-stu-id="5f951-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="5f951-433">服务类型</span><span class="sxs-lookup"><span data-stu-id="5f951-433">Service Type</span></span> | <span data-ttu-id="5f951-434">生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="5f951-435">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="5f951-436">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="5f951-437">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="5f951-438">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="5f951-439">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="5f951-440">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="5f951-441">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="5f951-442">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="5f951-443">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="5f951-444">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="5f951-445">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="5f951-446">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="5f951-447">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="5f951-448">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="5f951-449">其他资源</span><span class="sxs-lookup"><span data-stu-id="5f951-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="5f951-450">用于 DI 应用开发的 NDC 会议模式</span><span class="sxs-lookup"><span data-stu-id="5f951-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="5f951-451">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="5f951-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="5f951-452">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="5f951-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* <span data-ttu-id="5f951-453">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="5f951-453">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="5f951-454">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="5f951-454">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="5f951-455">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-455">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5f951-456">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="5f951-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="5f951-457">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="5f951-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="5f951-458">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="5f951-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="5f951-459">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5f951-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="5f951-460">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="5f951-460">Overview of dependency injection</span></span>

<span data-ttu-id="5f951-461">依赖项是指另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="5f951-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="5f951-462">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="5f951-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="5f951-463">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="5f951-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="5f951-464">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="5f951-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="5f951-465">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="5f951-466">代码依赖关系（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="5f951-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="5f951-467">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="5f951-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="5f951-468">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="5f951-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="5f951-469">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="5f951-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="5f951-470">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="5f951-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="5f951-471">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="5f951-472">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="5f951-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="5f951-473">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="5f951-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="5f951-474">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="5f951-475">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="5f951-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="5f951-476">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="5f951-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="5f951-477">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="5f951-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="5f951-478">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="5f951-479">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="5f951-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="5f951-480">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="5f951-481">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="5f951-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="5f951-482">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="5f951-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="5f951-483">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="5f951-484">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="5f951-485">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="5f951-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="5f951-486">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="5f951-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="5f951-487">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="5f951-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5f951-488">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="5f951-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="5f951-489">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="5f951-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="5f951-490">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="5f951-491">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="5f951-492">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="5f951-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="5f951-493">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="5f951-494">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="5f951-495">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="5f951-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="5f951-496">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="5f951-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="5f951-497">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="5f951-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="5f951-498">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="5f951-499">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="5f951-500">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="5f951-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="5f951-501">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-501">Services injected into Startup</span></span>

<span data-ttu-id="5f951-502">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="5f951-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="5f951-503">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="5f951-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="5f951-504">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="5f951-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="5f951-505">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-505">Framework-provided services</span></span>

<span data-ttu-id="5f951-506">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="5f951-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="5f951-507">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="5f951-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="5f951-508">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="5f951-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="5f951-509">下表列出了框架注册的服务的一小部分。</span><span class="sxs-lookup"><span data-stu-id="5f951-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="5f951-510">服务类型</span><span class="sxs-lookup"><span data-stu-id="5f951-510">Service Type</span></span> | <span data-ttu-id="5f951-511">生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="5f951-512">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="5f951-513">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="5f951-514">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="5f951-515">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="5f951-516">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="5f951-517">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="5f951-518">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="5f951-519">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="5f951-520">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="5f951-521">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="5f951-522">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="5f951-523">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="5f951-524">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="5f951-525">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="5f951-526">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="5f951-526">Register additional services with extension methods</span></span>

<span data-ttu-id="5f951-527">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="5f951-528">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="5f951-529">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="5f951-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="5f951-530">服务生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-530">Service lifetimes</span></span>

<span data-ttu-id="5f951-531">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="5f951-532">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="5f951-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="5f951-533">暂时</span><span class="sxs-lookup"><span data-stu-id="5f951-533">Transient</span></span>

<span data-ttu-id="5f951-534">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="5f951-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="5f951-535">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="5f951-536">在处理请求的应用中，在请求结束时会释放临时服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="5f951-537">作用域</span><span class="sxs-lookup"><span data-stu-id="5f951-537">Scoped</span></span>

<span data-ttu-id="5f951-538">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="5f951-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="5f951-539">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="5f951-540">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="5f951-541">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="5f951-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="5f951-542">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="5f951-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="5f951-543">单例</span><span class="sxs-lookup"><span data-stu-id="5f951-543">Singleton</span></span>

<span data-ttu-id="5f951-544">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="5f951-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="5f951-545">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="5f951-546">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="5f951-547">不要在类中实现单一实例设计模式并提供用户代码来管理对象的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="5f951-548">在处理请求的应用中，在应用关闭，释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="5f951-549">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="5f951-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="5f951-550">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="5f951-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="5f951-551">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="5f951-551">Service registration methods</span></span>

<span data-ttu-id="5f951-552">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="5f951-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="5f951-553">方法</span><span class="sxs-lookup"><span data-stu-id="5f951-553">Method</span></span> | <span data-ttu-id="5f951-554">自动</span><span class="sxs-lookup"><span data-stu-id="5f951-554">Automatic</span></span><br><span data-ttu-id="5f951-555">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="5f951-555">object</span></span><br><span data-ttu-id="5f951-556">处置</span><span class="sxs-lookup"><span data-stu-id="5f951-556">disposal</span></span> | <span data-ttu-id="5f951-557">多种</span><span class="sxs-lookup"><span data-stu-id="5f951-557">Multiple</span></span><br><span data-ttu-id="5f951-558">实现</span><span class="sxs-lookup"><span data-stu-id="5f951-558">implementations</span></span> | <span data-ttu-id="5f951-559">传递参数</span><span class="sxs-lookup"><span data-stu-id="5f951-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="5f951-560">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="5f951-561">是</span><span class="sxs-lookup"><span data-stu-id="5f951-561">Yes</span></span> | <span data-ttu-id="5f951-562">是</span><span class="sxs-lookup"><span data-stu-id="5f951-562">Yes</span></span> | <span data-ttu-id="5f951-563">否</span><span class="sxs-lookup"><span data-stu-id="5f951-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="5f951-564">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="5f951-565">是</span><span class="sxs-lookup"><span data-stu-id="5f951-565">Yes</span></span> | <span data-ttu-id="5f951-566">是</span><span class="sxs-lookup"><span data-stu-id="5f951-566">Yes</span></span> | <span data-ttu-id="5f951-567">是</span><span class="sxs-lookup"><span data-stu-id="5f951-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="5f951-568">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="5f951-569">是</span><span class="sxs-lookup"><span data-stu-id="5f951-569">Yes</span></span> | <span data-ttu-id="5f951-570">否</span><span class="sxs-lookup"><span data-stu-id="5f951-570">No</span></span> | <span data-ttu-id="5f951-571">否</span><span class="sxs-lookup"><span data-stu-id="5f951-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="5f951-572">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="5f951-573">否</span><span class="sxs-lookup"><span data-stu-id="5f951-573">No</span></span> | <span data-ttu-id="5f951-574">是</span><span class="sxs-lookup"><span data-stu-id="5f951-574">Yes</span></span> | <span data-ttu-id="5f951-575">是</span><span class="sxs-lookup"><span data-stu-id="5f951-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="5f951-576">示例：</span><span class="sxs-lookup"><span data-stu-id="5f951-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="5f951-577">否</span><span class="sxs-lookup"><span data-stu-id="5f951-577">No</span></span> | <span data-ttu-id="5f951-578">否</span><span class="sxs-lookup"><span data-stu-id="5f951-578">No</span></span> | <span data-ttu-id="5f951-579">是</span><span class="sxs-lookup"><span data-stu-id="5f951-579">Yes</span></span> |

<span data-ttu-id="5f951-580">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="5f951-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="5f951-581">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="5f951-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="5f951-582">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="5f951-583">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="5f951-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="5f951-584">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="5f951-585">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="5f951-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="5f951-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="5f951-587">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="5f951-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="5f951-588">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="5f951-589">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="5f951-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="5f951-590">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="5f951-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="5f951-591">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="5f951-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="5f951-592">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="5f951-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="5f951-593">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="5f951-593">Constructor injection behavior</span></span>

<span data-ttu-id="5f951-594">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="5f951-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="5f951-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="5f951-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="5f951-596">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="5f951-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="5f951-597">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="5f951-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="5f951-598">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="5f951-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="5f951-599">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="5f951-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="5f951-600">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="5f951-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="5f951-601">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="5f951-601">Entity Framework contexts</span></span>

<span data-ttu-id="5f951-602">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="5f951-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="5f951-603">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="5f951-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="5f951-604">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="5f951-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="5f951-605">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="5f951-605">Lifetime and registration options</span></span>

<span data-ttu-id="5f951-606">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="5f951-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="5f951-607">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="5f951-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="5f951-608">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="5f951-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="5f951-609">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="5f951-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="5f951-610">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="5f951-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="5f951-611">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="5f951-612">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="5f951-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="5f951-613">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="5f951-614">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="5f951-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="5f951-615">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="5f951-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="5f951-616">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="5f951-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="5f951-617">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="5f951-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="5f951-618">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="5f951-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="5f951-619">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="5f951-620">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="5f951-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="5f951-621">示例应用演示了各个请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="5f951-622">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="5f951-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="5f951-623">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="5f951-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="5f951-624">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="5f951-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="5f951-625">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="5f951-625">**First request:**</span></span>

<span data-ttu-id="5f951-626">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="5f951-626">Controller operations:</span></span>

<span data-ttu-id="5f951-627">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="5f951-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="5f951-628">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5f951-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5f951-629">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5f951-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5f951-630">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5f951-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5f951-631">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="5f951-631">`OperationService` operations:</span></span>

<span data-ttu-id="5f951-632">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="5f951-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="5f951-633">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="5f951-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="5f951-634">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5f951-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5f951-635">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5f951-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5f951-636">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="5f951-636">**Second request:**</span></span>

<span data-ttu-id="5f951-637">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="5f951-637">Controller operations:</span></span>

<span data-ttu-id="5f951-638">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="5f951-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="5f951-639">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5f951-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="5f951-640">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5f951-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5f951-641">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5f951-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5f951-642">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="5f951-642">`OperationService` operations:</span></span>

<span data-ttu-id="5f951-643">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="5f951-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="5f951-644">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="5f951-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="5f951-645">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="5f951-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="5f951-646">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="5f951-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="5f951-647">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="5f951-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="5f951-648">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="5f951-648">*Transient* objects are always different.</span></span> <span data-ttu-id="5f951-649">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="5f951-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="5f951-650">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="5f951-651">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="5f951-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="5f951-652">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="5f951-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="5f951-653">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="5f951-653">Call services from main</span></span>

<span data-ttu-id="5f951-654">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="5f951-655">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="5f951-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="5f951-656">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="5f951-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="5f951-657">作用域验证</span><span class="sxs-lookup"><span data-stu-id="5f951-657">Scope validation</span></span>

<span data-ttu-id="5f951-658">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="5f951-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="5f951-659">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="5f951-660">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="5f951-661">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="5f951-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="5f951-662">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="5f951-663">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="5f951-664">如果作用域服务创建于根容器，则该服务的生存期会实际上提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="5f951-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="5f951-665">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="5f951-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="5f951-666">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="5f951-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="5f951-667">请求服务</span><span class="sxs-lookup"><span data-stu-id="5f951-667">Request Services</span></span>

<span data-ttu-id="5f951-668">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="5f951-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="5f951-669">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="5f951-670">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="5f951-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="5f951-671">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="5f951-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="5f951-672">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="5f951-673">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="5f951-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="5f951-674">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="5f951-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="5f951-675">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-675">Design services for dependency injection</span></span>

<span data-ttu-id="5f951-676">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="5f951-676">Best practices are to:</span></span>

* <span data-ttu-id="5f951-677">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5f951-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="5f951-678">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="5f951-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="5f951-679">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="5f951-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="5f951-680">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="5f951-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="5f951-681">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="5f951-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="5f951-682">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="5f951-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="5f951-683">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="5f951-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="5f951-684">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="5f951-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="5f951-685">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="5f951-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="5f951-686">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="5f951-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="5f951-687">服务释放</span><span class="sxs-lookup"><span data-stu-id="5f951-687">Disposal of services</span></span>

<span data-ttu-id="5f951-688">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="5f951-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="5f951-689">如果实例是通过用户代码添加到容器中，则不会自动释放该实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="5f951-690">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="5f951-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="5f951-691">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5f951-691">In the following example:</span></span>

* <span data-ttu-id="5f951-692">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="5f951-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="5f951-693">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="5f951-694">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="5f951-695">服务如果未通过开发者代码显式释放，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="5f951-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="5f951-696">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="5f951-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="5f951-697">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-697">Transient, limited lifetime</span></span>

<span data-ttu-id="5f951-698">**方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-698">**Scenario**</span></span>

<span data-ttu-id="5f951-699">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="5f951-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="5f951-700">在根范围内解析实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="5f951-701">应在范围结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="5f951-702">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-702">**Solution**</span></span>

<span data-ttu-id="5f951-703">使用工厂模式在父范围外创建实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="5f951-704">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="5f951-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="5f951-705">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="5f951-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="5f951-706">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="5f951-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="5f951-707">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="5f951-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="5f951-708">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="5f951-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="5f951-709">**方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-709">**Scenario**</span></span>

<span data-ttu-id="5f951-710">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="5f951-711">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="5f951-711">**Solution**</span></span>

<span data-ttu-id="5f951-712">为实例注册范围内生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="5f951-713">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="5f951-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="5f951-714">使用范围的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="5f951-715">在生存期应结束时释放范围。</span><span class="sxs-lookup"><span data-stu-id="5f951-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="5f951-716">通用准则</span><span class="sxs-lookup"><span data-stu-id="5f951-716">General Guidelines</span></span>

* <span data-ttu-id="5f951-717">不要为 <xref:System.IDisposable> 实例注册暂时性范围。</span><span class="sxs-lookup"><span data-stu-id="5f951-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="5f951-718">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="5f951-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="5f951-719">不要在根范围内解析暂时性或范围内的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="5f951-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="5f951-720">唯一的一般性例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="5f951-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="5f951-721">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="5f951-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="5f951-722"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="5f951-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="5f951-723">范围应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="5f951-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="5f951-724">范围不区分层次，并且在各范围之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="5f951-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="5f951-725">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="5f951-725">Default service container replacement</span></span>

<span data-ttu-id="5f951-726">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="5f951-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="5f951-727">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="5f951-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="5f951-728">属性注入</span><span class="sxs-lookup"><span data-stu-id="5f951-728">Property injection</span></span>
* <span data-ttu-id="5f951-729">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="5f951-729">Injection based on name</span></span>
* <span data-ttu-id="5f951-730">子容器</span><span class="sxs-lookup"><span data-stu-id="5f951-730">Child containers</span></span>
* <span data-ttu-id="5f951-731">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="5f951-731">Custom lifetime management</span></span>
* <span data-ttu-id="5f951-732">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="5f951-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="5f951-733">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="5f951-733">Convention-based registration</span></span>

<span data-ttu-id="5f951-734">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="5f951-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="5f951-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="5f951-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="5f951-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="5f951-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="5f951-737">Grace</span><span class="sxs-lookup"><span data-stu-id="5f951-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="5f951-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="5f951-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="5f951-739">Lamar</span><span class="sxs-lookup"><span data-stu-id="5f951-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="5f951-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="5f951-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="5f951-741">Unity</span><span class="sxs-lookup"><span data-stu-id="5f951-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="5f951-742">线程安全</span><span class="sxs-lookup"><span data-stu-id="5f951-742">Thread safety</span></span>

<span data-ttu-id="5f951-743">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="5f951-744">如果单例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="5f951-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="5f951-745">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="5f951-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="5f951-746">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="5f951-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="5f951-747">建议</span><span class="sxs-lookup"><span data-stu-id="5f951-747">Recommendations</span></span>

* <span data-ttu-id="5f951-748">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="5f951-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="5f951-749">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="5f951-750">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="5f951-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="5f951-751">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="5f951-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="5f951-752">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="5f951-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="5f951-753">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="5f951-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="5f951-754">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="5f951-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="5f951-755">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="5f951-755">Avoid static access to services.</span></span> <span data-ttu-id="5f951-756">例如，避免静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="5f951-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="5f951-757">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="5f951-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="5f951-758">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="5f951-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="5f951-759">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="5f951-759">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="5f951-760">**正确**：</span><span class="sxs-lookup"><span data-stu-id="5f951-760">**Correct**:</span></span>

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

* <span data-ttu-id="5f951-761">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="5f951-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="5f951-762">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="5f951-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="5f951-763">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="5f951-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="5f951-764">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="5f951-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="5f951-765">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="5f951-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="5f951-766">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="5f951-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f951-767">其他资源</span><span class="sxs-lookup"><span data-stu-id="5f951-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="5f951-768">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="5f951-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="5f951-769">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="5f951-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* <span data-ttu-id="5f951-770">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="5f951-770">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="5f951-771">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="5f951-771">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="5f951-772">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="5f951-772">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
