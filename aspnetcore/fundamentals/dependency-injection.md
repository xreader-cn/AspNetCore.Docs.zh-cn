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
ms.openlocfilehash: 0a51647463362d6cfac335688d42d4be013f8b9c
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712503"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="07084-103">ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="07084-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07084-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="07084-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="07084-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="07084-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="07084-106">有关 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="07084-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="07084-107">有关选项的依赖项注入的详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="07084-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="07084-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07084-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="07084-109">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="07084-109">Overview of dependency injection</span></span>

<span data-ttu-id="07084-110">依赖项是指另一个对象所依赖的对象。</span><span class="sxs-lookup"><span data-stu-id="07084-110">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="07084-111">使用其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="07084-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="07084-112">类可以创建 `MyDependency` 类的实例，以便利用其 `WriteMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="07084-112">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="07084-113">在以下示例中，`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="07084-113">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="07084-114">该类创建并直接依赖于 `MyDependency` 类。</span><span class="sxs-lookup"><span data-stu-id="07084-114">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="07084-115">代码依赖项（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="07084-115">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="07084-116">要用不同的实现替换 `MyDependency`，必须修改 `IndexModel` 类。</span><span class="sxs-lookup"><span data-stu-id="07084-116">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="07084-117">如果 `MyDependency` 具有依赖项，则必须由 `IndexModel` 类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="07084-117">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="07084-118">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码将分散在整个应用中。</span><span class="sxs-lookup"><span data-stu-id="07084-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="07084-119">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="07084-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="07084-120">应用需使用模拟或存根 `MyDependency` 类，而该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="07084-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="07084-121">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="07084-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="07084-122">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="07084-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="07084-123">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="07084-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="07084-124">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="07084-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="07084-125">服务通常已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="07084-125">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="07084-126">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="07084-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="07084-127">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="07084-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="07084-128">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="07084-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="07084-129">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="07084-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="07084-130">示例应用使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="07084-130">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="07084-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 方法使用范围内生存期（单个请求的生存期）注册服务。</span><span class="sxs-lookup"><span data-stu-id="07084-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="07084-132">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="07084-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="07084-133">在示例应用中，请求 `IMyDependency` 服务并用于调用 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="07084-133">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="07084-134">通过使用 DI 模式，表示控制器：</span><span class="sxs-lookup"><span data-stu-id="07084-134">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="07084-135">不使用具体类型 `MyDependency`，仅使用它实现的 `IMyDependency` 接口。</span><span class="sxs-lookup"><span data-stu-id="07084-135">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="07084-136">这样可以轻松地更改控制器使用的实现，而无需修改控制器。</span><span class="sxs-lookup"><span data-stu-id="07084-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="07084-137">不创建 `MyDependency` 的实例，这由 DI 容器创建。</span><span class="sxs-lookup"><span data-stu-id="07084-137">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="07084-138">可以通过使用内置日志记录 API 来改善 `IMyDependency` 接口的实现：</span><span class="sxs-lookup"><span data-stu-id="07084-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="07084-139">更新的 `ConfigureServices` 方法注册新的 `IMyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="07084-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="07084-140">`MyDependency2` 依赖于 <xref:Microsoft.Extensions.Logging.ILogger%601>，并在构造函数中对其进行请求。</span><span class="sxs-lookup"><span data-stu-id="07084-140">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="07084-141">`ILogger<TCategoryName>` 是[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="07084-141">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="07084-142">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="07084-142">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="07084-143">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="07084-143">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="07084-144">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-144">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="07084-145">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="07084-145">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="07084-146">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="07084-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="07084-147">在依赖项注入术语中，服务：</span><span class="sxs-lookup"><span data-stu-id="07084-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="07084-148">通常是向其他对象提供服务的对象，如 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="07084-148">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="07084-149">与 Web 服务无关，尽管服务可能使用 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="07084-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="07084-150">框架提供可靠的[日志记录](xref:fundamentals/logging/index)系统。</span><span class="sxs-lookup"><span data-stu-id="07084-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="07084-151">编写上述示例中的 `IMyDependency` 实现来演示基本的 DI，而不是来实现日志记录。</span><span class="sxs-lookup"><span data-stu-id="07084-151">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="07084-152">大多数应用都不需要编写记录器。</span><span class="sxs-lookup"><span data-stu-id="07084-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="07084-153">下面的代码演示如何使用默认日志记录，这不要求在 `ConfigureServices` 中注册任何服务：</span><span class="sxs-lookup"><span data-stu-id="07084-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="07084-154">使用前面的代码时，无需更新 `ConfigureServices`，因为框架提供[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="07084-154">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="07084-155">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="07084-155">Services injected into Startup</span></span>

<span data-ttu-id="07084-156">服务可以注入 `Startup` 构造函数和 `Startup.Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="07084-156">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="07084-157">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="07084-157">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="07084-158">任何向 DI 容器注册的服务都可以注入 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="07084-158">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="07084-159">有关详细信息，请参阅 <xref:fundamentals/startup> 和[访问 Startup 中的配置](xref:fundamentals/configuration/index#access-configuration-in-startup)。</span><span class="sxs-lookup"><span data-stu-id="07084-159">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="07084-160">使用扩展方法注册服务组</span><span class="sxs-lookup"><span data-stu-id="07084-160">Register groups of services with extension methods</span></span>

<span data-ttu-id="07084-161">ASP.NET Core 框架使用一种约定来注册一组相关服务。</span><span class="sxs-lookup"><span data-stu-id="07084-161">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="07084-162">约定使用单个 `Add{GROUP_NAME}` 扩展方法来注册该框架功能所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="07084-162">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="07084-163">例如，<Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> 扩展方法会注册 MVC 控制器所需的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-163">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="07084-164">下面的代码通过个人用户帐户由 Razor 页面模板生成，并演示如何使用扩展方法 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> 将其他服务添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="07084-164">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="07084-165">服务生存期</span><span class="sxs-lookup"><span data-stu-id="07084-165">Service lifetimes</span></span>

<span data-ttu-id="07084-166">可以使用以下任一生存期注册服务：</span><span class="sxs-lookup"><span data-stu-id="07084-166">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="07084-167">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-167">Transient</span></span>
* <span data-ttu-id="07084-168">作用域</span><span class="sxs-lookup"><span data-stu-id="07084-168">Scoped</span></span>
* <span data-ttu-id="07084-169">单例</span><span class="sxs-lookup"><span data-stu-id="07084-169">Singleton</span></span>

<span data-ttu-id="07084-170">下列各部分描述了上述每个生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-170">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="07084-171">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-171">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="07084-172">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-172">Transient</span></span>

<span data-ttu-id="07084-173">暂时生存期服务是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="07084-173">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="07084-174">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-174">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="07084-175">向 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> 注册暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="07084-175">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="07084-176">在处理请求的应用中，在请求结束时会释放暂时服务。</span><span class="sxs-lookup"><span data-stu-id="07084-176">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="07084-177">作用域</span><span class="sxs-lookup"><span data-stu-id="07084-177">Scoped</span></span>

<span data-ttu-id="07084-178">作用域生存期服务针对每个客户端请求（连接）创建一次。</span><span class="sxs-lookup"><span data-stu-id="07084-178">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="07084-179">向 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 注册范围内服务。</span><span class="sxs-lookup"><span data-stu-id="07084-179">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="07084-180">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-180">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="07084-181">使用 Entity Framework Core 时，默认情况下 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展方法使用范围内生存期来注册 `DbContext` 类型。</span><span class="sxs-lookup"><span data-stu-id="07084-181">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="07084-182">不要从单一实例解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="07084-182">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="07084-183">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="07084-183">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="07084-184">可以：</span><span class="sxs-lookup"><span data-stu-id="07084-184">It's fine to:</span></span>

* <span data-ttu-id="07084-185">从范围内或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="07084-185">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="07084-186">从其他范围内或暂时性服务解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="07084-186">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="07084-187">默认情况下在开发环境中，从具有较长生存期的其他服务解析服务将引发异常。</span><span class="sxs-lookup"><span data-stu-id="07084-187">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="07084-188">有关详细信息，请参阅[作用域验证](#sv)。</span><span class="sxs-lookup"><span data-stu-id="07084-188">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="07084-189">要在中间件中使用范围内服务，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="07084-189">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="07084-190">将服务注入中间件的 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="07084-190">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="07084-191">使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)会引发运行时异常，因为它强制使范围内服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="07084-191">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="07084-192">[生存期和注册选项](#lifetime-and-registration-options)部分中的示例演示了 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="07084-192">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="07084-193">使用[基于工厂的中间件](xref:fundamentals/middleware/extensibility)。</span><span class="sxs-lookup"><span data-stu-id="07084-193">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="07084-194">使用此方法注册的中间件按客户端请求（连接）激活，这也使范围内服务可注入中间件的 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="07084-194">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="07084-195">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="07084-195">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="07084-196">单例</span><span class="sxs-lookup"><span data-stu-id="07084-196">Singleton</span></span>

<span data-ttu-id="07084-197">创建单例生命周期服务的情况如下：</span><span class="sxs-lookup"><span data-stu-id="07084-197">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="07084-198">在首次请求它们时进行创建；或者</span><span class="sxs-lookup"><span data-stu-id="07084-198">The first time they're requested.</span></span>
* <span data-ttu-id="07084-199">在向容器直接提供实现实例时由开发人员进行创建。</span><span class="sxs-lookup"><span data-stu-id="07084-199">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="07084-200">很少用到此方法。</span><span class="sxs-lookup"><span data-stu-id="07084-200">This approach is rarely needed.</span></span>

<span data-ttu-id="07084-201">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="07084-201">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="07084-202">如果应用需要单一实例行为，则允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-202">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="07084-203">不要实现单一实例设计模式，或提供代码来释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="07084-203">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="07084-204">服务永远不应由解析容器服务的代码释放。</span><span class="sxs-lookup"><span data-stu-id="07084-204">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="07084-205">如果类型或工厂注册为单一实例，则容器自动释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="07084-205">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="07084-206">向 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> 注册单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="07084-206">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="07084-207">单一实例服务必须是线程安全的，并且通常在无状态服务中使用。</span><span class="sxs-lookup"><span data-stu-id="07084-207">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="07084-208">在处理请求的应用中，当应用关闭并释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="07084-208">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="07084-209">由于应用关闭之前不释放内存，因此请考虑单一实例服务的内存使用。</span><span class="sxs-lookup"><span data-stu-id="07084-209">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="07084-210">不要从单一实例解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="07084-210">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="07084-211">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="07084-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="07084-212">可以从范围内或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="07084-212">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="07084-213">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="07084-213">Service registration methods</span></span>

<span data-ttu-id="07084-214">框架提供了适用于特定场景的服务注册扩展方法：</span><span class="sxs-lookup"><span data-stu-id="07084-214">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="07084-215">方法</span><span class="sxs-lookup"><span data-stu-id="07084-215">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="07084-216">自动</span><span class="sxs-lookup"><span data-stu-id="07084-216">Automatic</span></span><br><span data-ttu-id="07084-217">对象</span><span class="sxs-lookup"><span data-stu-id="07084-217">object</span></span><br><span data-ttu-id="07084-218">释放</span><span class="sxs-lookup"><span data-stu-id="07084-218">disposal</span></span> | <span data-ttu-id="07084-219">多种</span><span class="sxs-lookup"><span data-stu-id="07084-219">Multiple</span></span><br><span data-ttu-id="07084-220">实现</span><span class="sxs-lookup"><span data-stu-id="07084-220">implementations</span></span> | <span data-ttu-id="07084-221">传递参数</span><span class="sxs-lookup"><span data-stu-id="07084-221">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="07084-222">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-222">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="07084-223">“是”</span><span class="sxs-lookup"><span data-stu-id="07084-223">Yes</span></span>                             | <span data-ttu-id="07084-224">是</span><span class="sxs-lookup"><span data-stu-id="07084-224">Yes</span></span>                         | <span data-ttu-id="07084-225">否</span><span class="sxs-lookup"><span data-stu-id="07084-225">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="07084-226">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-226">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="07084-227">是</span><span class="sxs-lookup"><span data-stu-id="07084-227">Yes</span></span>                             | <span data-ttu-id="07084-228">是</span><span class="sxs-lookup"><span data-stu-id="07084-228">Yes</span></span>                         | <span data-ttu-id="07084-229">是</span><span class="sxs-lookup"><span data-stu-id="07084-229">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="07084-230">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-230">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="07084-231">是</span><span class="sxs-lookup"><span data-stu-id="07084-231">Yes</span></span>                             | <span data-ttu-id="07084-232">否</span><span class="sxs-lookup"><span data-stu-id="07084-232">No</span></span>                          | <span data-ttu-id="07084-233">否</span><span class="sxs-lookup"><span data-stu-id="07084-233">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="07084-234">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-234">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="07084-235">否</span><span class="sxs-lookup"><span data-stu-id="07084-235">No</span></span>                              | <span data-ttu-id="07084-236">是</span><span class="sxs-lookup"><span data-stu-id="07084-236">Yes</span></span>                         | <span data-ttu-id="07084-237">是</span><span class="sxs-lookup"><span data-stu-id="07084-237">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="07084-238">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-238">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="07084-239">否</span><span class="sxs-lookup"><span data-stu-id="07084-239">No</span></span>                              | <span data-ttu-id="07084-240">否</span><span class="sxs-lookup"><span data-stu-id="07084-240">No</span></span>                          | <span data-ttu-id="07084-241">是</span><span class="sxs-lookup"><span data-stu-id="07084-241">Yes</span></span>       |

<span data-ttu-id="07084-242">要详细了解释放类型，请参阅[服务释放](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="07084-242">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="07084-243">在[为测试模拟类型](xref:test/integration-tests#inject-mock-services)时，使用多个实现很常见。</span><span class="sxs-lookup"><span data-stu-id="07084-243">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="07084-244">框架还提供 `TryAdd{LIFETIME}` 扩展方法，只有当尚未注册某个实现时，才注册该服务。</span><span class="sxs-lookup"><span data-stu-id="07084-244">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="07084-245">在下面的示例中，对 `AddSingleton` 的调用会将 `MyDependency` 注册为 `IMyDependency`的实现。</span><span class="sxs-lookup"><span data-stu-id="07084-245">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="07084-246">对 `TryAddSingleton` 的调用没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="07084-246">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="07084-247">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="07084-247">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="07084-248">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) 方法仅会在没有同一类型实现的情况下才注册该服务。</span><span class="sxs-lookup"><span data-stu-id="07084-248">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="07084-249">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="07084-249">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="07084-250">注册服务时，开发人员应在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="07084-250">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="07084-251">通常库作者使用 `TryAddEnumerable` 来避免在容器中注册实现的多个副本。</span><span class="sxs-lookup"><span data-stu-id="07084-251">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="07084-252">在下面的示例中，对 `TryAddEnumerable` 的第一次调用会将 `MyDependency` 注册为 `IMyDependency1`的实现。</span><span class="sxs-lookup"><span data-stu-id="07084-252">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="07084-253">第二次调用向 `IMyDependency2` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="07084-253">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="07084-254">第三次调用没有任何作用，因为 `IMyDependency1` 已有一个 `MyDependency` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="07084-254">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="07084-255">服务注册通常与顺序无关，除了注册同一类型的多个实现时。</span><span class="sxs-lookup"><span data-stu-id="07084-255">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="07084-256">`IServiceCollection` 是 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 对象的集合。</span><span class="sxs-lookup"><span data-stu-id="07084-256">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="07084-257">以下示例演示如何通过创建和添加 `ServiceDescriptor` 来注册服务：</span><span class="sxs-lookup"><span data-stu-id="07084-257">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="07084-258">内置 `Add{LIFETIME}` 方法使用同一种方式。</span><span class="sxs-lookup"><span data-stu-id="07084-258">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="07084-259">相关示例请参阅 [AddScoped 源代码](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)。</span><span class="sxs-lookup"><span data-stu-id="07084-259">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="07084-260">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="07084-260">Constructor injection behavior</span></span>

<span data-ttu-id="07084-261">服务可使用以下方式来解析：</span><span class="sxs-lookup"><span data-stu-id="07084-261">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="07084-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="07084-262"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="07084-263">创建未在容器中注册的对象。</span><span class="sxs-lookup"><span data-stu-id="07084-263">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="07084-264">与框架功能一起使用，例如[标记帮助程序](xref:mvc/views/tag-helpers/intro)、[MVC 控制器](xref:mvc/models/model-binding)和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="07084-264">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="07084-265">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="07084-265">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="07084-266">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="07084-266">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="07084-267">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="07084-267">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="07084-268">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="07084-268">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="07084-269">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="07084-269">Entity Framework contexts</span></span>

<span data-ttu-id="07084-270">默认情况下，使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="07084-270">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="07084-271">要使用其他生存期，请使用 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 重载来指定生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-271">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="07084-272">给定生存期的服务不应使用生存期比服务生存期短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="07084-272">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="07084-273">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="07084-273">Lifetime and registration options</span></span>

<span data-ttu-id="07084-274">为了演示服务生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="07084-274">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="07084-275">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="07084-275">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="07084-276">以下 `Operation` 类实现了前面的所有接口。</span><span class="sxs-lookup"><span data-stu-id="07084-276">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="07084-277">`Operation` 构造函数生成 GUID，并将最后 4 个字符存储在 `OperationId` 属性中：</span><span class="sxs-lookup"><span data-stu-id="07084-277">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="07084-278">`Startup.ConfigureServices` 方法根据命名生存期创建 `Operation` 类的多个注册：</span><span class="sxs-lookup"><span data-stu-id="07084-278">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="07084-279">示例应用一并演示了请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-279">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="07084-280">`IndexModel` 和中间件请求每种 `IOperation` 类型，并记录各自的 `OperationId`：</span><span class="sxs-lookup"><span data-stu-id="07084-280">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="07084-281">与 `IndexModel` 类似，中间件会解析相同的服务：</span><span class="sxs-lookup"><span data-stu-id="07084-281">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="07084-282">范围内服务必须在 `InvokeAsync` 方法中进行解析：</span><span class="sxs-lookup"><span data-stu-id="07084-282">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="07084-283">记录器输出显示：</span><span class="sxs-lookup"><span data-stu-id="07084-283">The logger output shows:</span></span>

* <span data-ttu-id="07084-284">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="07084-284">*Transient* objects are always different.</span></span> <span data-ttu-id="07084-285">`IndexModel` 和中间件中的临时 `OperationId` 值不同。</span><span class="sxs-lookup"><span data-stu-id="07084-285">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="07084-286">范围内对象对每个请求而言是相同的，但在请求之间不同。</span><span class="sxs-lookup"><span data-stu-id="07084-286">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="07084-287">单一实例对象对于每个请求是相同的。</span><span class="sxs-lookup"><span data-stu-id="07084-287">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="07084-288">若要减少日志记录输出，请在 appsettings.Development.json 文件中设置“Logging:LogLevel:Microsoft:Error”：</span><span class="sxs-lookup"><span data-stu-id="07084-288">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="07084-289">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="07084-289">Call services from main</span></span>

<span data-ttu-id="07084-290">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的作用域服务。 </span><span class="sxs-lookup"><span data-stu-id="07084-290">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="07084-291">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="07084-291">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="07084-292">以下示例演示如何访问范围内 `IMyDependency` 服务并在 `Program.Main` 中调用其 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="07084-292">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="07084-293">作用域验证</span><span class="sxs-lookup"><span data-stu-id="07084-293">Scope validation</span></span>

<span data-ttu-id="07084-294">如果应用在[开发环境](xref:fundamentals/environments)中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 以生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="07084-294">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="07084-295">没有从根服务提供程序解析到范围内服务。</span><span class="sxs-lookup"><span data-stu-id="07084-295">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="07084-296">未将范围内服务注入单一实例。</span><span class="sxs-lookup"><span data-stu-id="07084-296">Scoped services aren't injected into singletons.</span></span>
* <span data-ttu-id="07084-297">未将暂时性服务注入单一实例或范围内服务。</span><span class="sxs-lookup"><span data-stu-id="07084-297">Transient services aren't injected into singletons or scoped services.</span></span>

<span data-ttu-id="07084-298">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="07084-298">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="07084-299">在启动提供程序和应用时，根服务提供程序的生存期对应于应用的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="07084-299">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="07084-300">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="07084-300">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="07084-301">如果范围内服务创建于根容器，则该服务的生存期实际上提升至单一实例，因为根容器只会在应用关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="07084-301">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="07084-302">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="07084-302">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="07084-303">有关详细信息，请参阅[作用域验证](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="07084-303">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="07084-304">请求服务</span><span class="sxs-lookup"><span data-stu-id="07084-304">Request Services</span></span>

<span data-ttu-id="07084-305">ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="07084-305">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="07084-306">从请求内部请求服务时，将从 `RequestServices` 集合解析服务及其依赖项。</span><span class="sxs-lookup"><span data-stu-id="07084-306">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="07084-307">为每个请求创建一个作用域，`RequestServices` 公开作用域服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="07084-307">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="07084-308">只要请求处于活动状态，所有作用域服务都有效。</span><span class="sxs-lookup"><span data-stu-id="07084-308">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="07084-309">与解析 `RequestServices` 集合中的服务相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="07084-309">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="07084-310">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="07084-310">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="07084-311">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="07084-311">Design services for dependency injection</span></span>

<span data-ttu-id="07084-312">在设计能够进行依赖注入的服务时：</span><span class="sxs-lookup"><span data-stu-id="07084-312">When designing services for dependency injection:</span></span>

* <span data-ttu-id="07084-313">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="07084-313">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="07084-314">通过将应用设计为改用单一实例服务，避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="07084-314">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="07084-315">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="07084-315">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="07084-316">直接实例化会将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="07084-316">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="07084-317">不在服务中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="07084-317">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="07084-318">如果一个类有过多注入依赖项，这可能表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="07084-318">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="07084-319">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="07084-319">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="07084-320">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="07084-320">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="07084-321">服务释放</span><span class="sxs-lookup"><span data-stu-id="07084-321">Disposal of services</span></span>

<span data-ttu-id="07084-322">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="07084-322">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="07084-323">从容器中解析的服务绝对不应由开发人员释放。</span><span class="sxs-lookup"><span data-stu-id="07084-323">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="07084-324">如果类型或工厂注册为单一实例，则容器自动释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="07084-324">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="07084-325">在下面的示例中，服务由服务容器创建，并自动释放：</span><span class="sxs-lookup"><span data-stu-id="07084-325">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="07084-326">每次刷新索引页后，调试控制台显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="07084-326">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="07084-327">不由服务容器创建的服务</span><span class="sxs-lookup"><span data-stu-id="07084-327">Services not created by the service container</span></span>

<span data-ttu-id="07084-328">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="07084-328">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="07084-329">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="07084-329">In the preceding code:</span></span>

* <span data-ttu-id="07084-330">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="07084-330">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="07084-331">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="07084-331">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="07084-332">开发人员负责释放服务。</span><span class="sxs-lookup"><span data-stu-id="07084-332">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="07084-333">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="07084-333">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="07084-334">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="07084-334">Transient, limited lifetime</span></span>

<span data-ttu-id="07084-335">**方案**</span><span class="sxs-lookup"><span data-stu-id="07084-335">**Scenario**</span></span>

<span data-ttu-id="07084-336">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="07084-336">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="07084-337">在根范围（根容器）内解析实例。</span><span class="sxs-lookup"><span data-stu-id="07084-337">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="07084-338">应在作用域结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="07084-338">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="07084-339">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="07084-339">**Solution**</span></span>

<span data-ttu-id="07084-340">使用工厂模式在父作用域外创建实例。 </span><span class="sxs-lookup"><span data-stu-id="07084-340">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="07084-341">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="07084-341">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="07084-342">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="07084-342">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="07084-343">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="07084-343">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="07084-344">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="07084-344">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="07084-345">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="07084-345">Shared instance, limited lifetime</span></span>

<span data-ttu-id="07084-346">**方案**</span><span class="sxs-lookup"><span data-stu-id="07084-346">**Scenario**</span></span>

<span data-ttu-id="07084-347">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 实例应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-347">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="07084-348">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="07084-348">**Solution**</span></span>

<span data-ttu-id="07084-349">为实例注册作用域生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-349">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="07084-350">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 创建新 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="07084-350">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="07084-351">使用作用域的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-351">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="07084-352">如果不再需要范围，请将其释放。</span><span class="sxs-lookup"><span data-stu-id="07084-352">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="07084-353">一般 IDisposable 准则</span><span class="sxs-lookup"><span data-stu-id="07084-353">General IDisposable guidelines</span></span>

* <span data-ttu-id="07084-354">不要为 <xref:System.IDisposable> 实例注册暂时性生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-354">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="07084-355">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="07084-355">Use the factory pattern instead.</span></span>
* <span data-ttu-id="07084-356">不要在根范围内解析具有暂时性或范围内生存期的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="07084-356">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="07084-357">唯一的例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，但这不是理想模式。</span><span class="sxs-lookup"><span data-stu-id="07084-357">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="07084-358">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="07084-358">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="07084-359"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="07084-359">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="07084-360">使用范围控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-360">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="07084-361"> 作用域不区分层次，并且在各作用域之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="07084-361">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="07084-362">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="07084-362">Default service container replacement</span></span>

<span data-ttu-id="07084-363">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="07084-363">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="07084-364">我们建议使用内置容器，除非你需要的特定功能不受它支持，例如：</span><span class="sxs-lookup"><span data-stu-id="07084-364">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="07084-365">属性注入</span><span class="sxs-lookup"><span data-stu-id="07084-365">Property injection</span></span>
* <span data-ttu-id="07084-366">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="07084-366">Injection based on name</span></span>
* <span data-ttu-id="07084-367">子容器</span><span class="sxs-lookup"><span data-stu-id="07084-367">Child containers</span></span>
* <span data-ttu-id="07084-368">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="07084-368">Custom lifetime management</span></span>
* <span data-ttu-id="07084-369">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="07084-369">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="07084-370">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="07084-370">Convention-based registration</span></span>

<span data-ttu-id="07084-371">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="07084-371">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="07084-372">Autofac</span><span class="sxs-lookup"><span data-stu-id="07084-372">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="07084-373">DryIoc</span><span class="sxs-lookup"><span data-stu-id="07084-373">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="07084-374">Grace</span><span class="sxs-lookup"><span data-stu-id="07084-374">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="07084-375">LightInject</span><span class="sxs-lookup"><span data-stu-id="07084-375">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="07084-376">Lamar</span><span class="sxs-lookup"><span data-stu-id="07084-376">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="07084-377">Stashbox</span><span class="sxs-lookup"><span data-stu-id="07084-377">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="07084-378">Unity</span><span class="sxs-lookup"><span data-stu-id="07084-378">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="07084-379">线程安全</span><span class="sxs-lookup"><span data-stu-id="07084-379">Thread safety</span></span>

<span data-ttu-id="07084-380">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="07084-380">Create thread-safe singleton services.</span></span> <span data-ttu-id="07084-381">如果单一实例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单一实例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="07084-381">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="07084-382">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="07084-382">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="07084-383">像类型 (`static`) 构造函数一样，它保证仅由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="07084-383">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="07084-384">建议</span><span class="sxs-lookup"><span data-stu-id="07084-384">Recommendations</span></span>

* <span data-ttu-id="07084-385">不支持基于`async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="07084-385">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="07084-386">由于 C# 不支持异步构造函数，因此请在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="07084-386">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="07084-387">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="07084-387">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="07084-388">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="07084-388">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="07084-389">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="07084-389">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="07084-390">同样，避免“数据持有者”对象，也就是仅仅为实现对另一个对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="07084-390">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="07084-391">最好通过 DI 请求实际项。</span><span class="sxs-lookup"><span data-stu-id="07084-391">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="07084-392">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="07084-392">Avoid static access to services.</span></span> <span data-ttu-id="07084-393">例如，避免将 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 捕获为静态字段或属性以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="07084-393">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="07084-394">使 DI 工厂保持快速且同步。</span><span class="sxs-lookup"><span data-stu-id="07084-394">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="07084-395">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="07084-395">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="07084-396">例如，可以使用 DI 代替时，不要调用 <xref:System.IServiceProvider.GetService%2A>  来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="07084-396">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="07084-397">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="07084-397">**Incorrect:**</span></span>

    ![错误代码](dependency-injection/_static/bad.png)

  <span data-ttu-id="07084-399">**正确**：</span><span class="sxs-lookup"><span data-stu-id="07084-399">**Correct**:</span></span>

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
* <span data-ttu-id="07084-400">要避免的另一个服务定位器变体是注入需在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="07084-400">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="07084-401">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="07084-401">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="07084-402">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="07084-402">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="07084-403">避免在 `ConfigureServices` 中调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>。</span><span class="sxs-lookup"><span data-stu-id="07084-403">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="07084-404">当开发人员想要在 `ConfigureServices` 中解析服务时，通常会调用 `BuildServiceProvider`。</span><span class="sxs-lookup"><span data-stu-id="07084-404">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="07084-405">例如，假设 `LoginPath` 从配置中加载。</span><span class="sxs-lookup"><span data-stu-id="07084-405">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="07084-406">避免采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="07084-406">Avoid the following approach:</span></span>

  ![调用 BuildServiceProvider 的错误代码](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="07084-408">在上图中，选择 `services.BuildServiceProvider` 下的绿色波浪线将显示以下 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="07084-408">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="07084-409">ASP0000 从应用程序代码调用“BuildServiceProvider”会导致创建单一实例服务的其他副本。</span><span class="sxs-lookup"><span data-stu-id="07084-409">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="07084-410">考虑依赖项注入服务等替代项作为“Configure”的参数。</span><span class="sxs-lookup"><span data-stu-id="07084-410">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="07084-411">调用 `BuildServiceProvider` 会创建第二个容器，该容器可创建残缺的单一实例并导致跨多个容器引用对象图。</span><span class="sxs-lookup"><span data-stu-id="07084-411">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="07084-412">获取 `LoginPath` 的正确方法是使用选项模式对 DI 的内置支持：</span><span class="sxs-lookup"><span data-stu-id="07084-412">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="07084-413">可释放的暂时性服务由容器捕获以进行释放。</span><span class="sxs-lookup"><span data-stu-id="07084-413">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="07084-414">如果从顶级容器解析，这会变为内存泄漏。</span><span class="sxs-lookup"><span data-stu-id="07084-414">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="07084-415">启用范围验证，确保应用没有捕获单一实例的范围内服务。</span><span class="sxs-lookup"><span data-stu-id="07084-415">Enable scope validation to make sure the app doesn't have scoped services that capture singletons.</span></span> <span data-ttu-id="07084-416">有关详细信息，请参阅[作用域验证](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="07084-416">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="07084-417">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="07084-417">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="07084-418">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="07084-418">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="07084-419">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="07084-419">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="07084-420">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="07084-420">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="07084-421">DI 中适用于多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="07084-421">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="07084-422">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 是用于在 ASP.NET Core 上构建模块化多租户应用程序的应用程序框架。</span><span class="sxs-lookup"><span data-stu-id="07084-422">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="07084-423">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="07084-423">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="07084-424">请参阅 [Orchard Core 示例](https://github.com/OrchardCMS/OrchardCore.Samples)，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="07084-424">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="07084-425">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="07084-425">Framework-provided services</span></span>

<span data-ttu-id="07084-426">`Startup.ConfigureServices` 方法注册应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="07084-426">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="07084-427">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="07084-427">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="07084-428">对于基于 ASP.NET Core 模板的应用，该框架会注册 250 个以上的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-428">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="07084-429">下表列出了框架注册的这些服务的一小部分：</span><span class="sxs-lookup"><span data-stu-id="07084-429">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="07084-430">服务类型</span><span class="sxs-lookup"><span data-stu-id="07084-430">Service Type</span></span>                                                                                    | <span data-ttu-id="07084-431">生存期</span><span class="sxs-lookup"><span data-stu-id="07084-431">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="07084-432">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-432">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="07084-433">单例</span><span class="sxs-lookup"><span data-stu-id="07084-433">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="07084-434">单例</span><span class="sxs-lookup"><span data-stu-id="07084-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="07084-435">单例</span><span class="sxs-lookup"><span data-stu-id="07084-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="07084-436">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-436">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="07084-437">单例</span><span class="sxs-lookup"><span data-stu-id="07084-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="07084-438">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-438">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="07084-439">单例</span><span class="sxs-lookup"><span data-stu-id="07084-439">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="07084-440">单例</span><span class="sxs-lookup"><span data-stu-id="07084-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="07084-441">单例</span><span class="sxs-lookup"><span data-stu-id="07084-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="07084-442">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-442">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="07084-443">单例</span><span class="sxs-lookup"><span data-stu-id="07084-443">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="07084-444">单例</span><span class="sxs-lookup"><span data-stu-id="07084-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="07084-445">单例</span><span class="sxs-lookup"><span data-stu-id="07084-445">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="07084-446">其他资源</span><span class="sxs-lookup"><span data-stu-id="07084-446">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="07084-447">用于 DI 应用开发的 NDC 会议模式</span><span class="sxs-lookup"><span data-stu-id="07084-447">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="07084-448">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="07084-448">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="07084-449">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="07084-449">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="07084-450">显式依赖关系原则</span><span class="sxs-lookup"><span data-stu-id="07084-450">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="07084-451">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="07084-451">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="07084-452">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="07084-452">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07084-453">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="07084-453">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="07084-454">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="07084-454">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="07084-455">有关 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="07084-455">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="07084-456">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07084-456">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="07084-457">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="07084-457">Overview of dependency injection</span></span>

<span data-ttu-id="07084-458">依赖项是指另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="07084-458">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="07084-459">查看以下具有 `WriteMessage` 方法的 `MyDependency` 类，此类为应用中其他类所依赖：</span><span class="sxs-lookup"><span data-stu-id="07084-459">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="07084-460">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于其他类。</span><span class="sxs-lookup"><span data-stu-id="07084-460">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="07084-461">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="07084-461">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="07084-462">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="07084-462">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="07084-463">代码依赖关系（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="07084-463">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="07084-464">若要用另一个实现替换 `MyDependency`，则必须修改类。</span><span class="sxs-lookup"><span data-stu-id="07084-464">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="07084-465">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="07084-465">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="07084-466">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码将分散在整个应用中。</span><span class="sxs-lookup"><span data-stu-id="07084-466">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="07084-467">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="07084-467">This implementation is difficult to unit test.</span></span> <span data-ttu-id="07084-468">应用需使用模拟或存根 `MyDependency` 类，而该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="07084-468">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="07084-469">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="07084-469">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="07084-470">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="07084-470">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="07084-471">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="07084-471">Registration of the dependency in a service container.</span></span> <span data-ttu-id="07084-472">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="07084-472">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="07084-473">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="07084-473">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="07084-474">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="07084-474">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="07084-475">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="07084-475">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="07084-476">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="07084-476">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="07084-477">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="07084-477">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="07084-478">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="07084-478">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="07084-479">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="07084-479">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="07084-480">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="07084-480">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="07084-481">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-481">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="07084-482">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="07084-482">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="07084-483">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="07084-483">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="07084-484">`IMyDependency` 在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="07084-484">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="07084-485">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="07084-485">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="07084-486">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="07084-486">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="07084-487">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="07084-487">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="07084-488">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-488">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="07084-489">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="07084-489">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="07084-490">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="07084-490">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="07084-491">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-491">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="07084-492">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="07084-492">We recommended that apps follow this convention.</span></span> <span data-ttu-id="07084-493">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册组。</span><span class="sxs-lookup"><span data-stu-id="07084-493">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="07084-494">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="07084-494">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="07084-495">可通过类的构造函数请求服务实例，在该函数中可使用服务并将其分配给私有字段。</span><span class="sxs-lookup"><span data-stu-id="07084-495">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="07084-496">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="07084-496">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="07084-497">在示例应用中，请求 `IMyDependency` 实例并将其用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="07084-497">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="07084-498">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="07084-498">Services injected into Startup</span></span>

<span data-ttu-id="07084-499">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="07084-499">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="07084-500">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="07084-500">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="07084-501">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="07084-501">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="07084-502">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="07084-502">Framework-provided services</span></span>

<span data-ttu-id="07084-503">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="07084-503">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="07084-504">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="07084-504">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="07084-505">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="07084-505">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="07084-506">下表列出了框架注册的服务的一小部分。</span><span class="sxs-lookup"><span data-stu-id="07084-506">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="07084-507">服务类型</span><span class="sxs-lookup"><span data-stu-id="07084-507">Service Type</span></span> | <span data-ttu-id="07084-508">生存期</span><span class="sxs-lookup"><span data-stu-id="07084-508">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="07084-509">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-509">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="07084-510">单例</span><span class="sxs-lookup"><span data-stu-id="07084-510">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="07084-511">单例</span><span class="sxs-lookup"><span data-stu-id="07084-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="07084-512">单例</span><span class="sxs-lookup"><span data-stu-id="07084-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="07084-513">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-513">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="07084-514">单例</span><span class="sxs-lookup"><span data-stu-id="07084-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="07084-515">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-515">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="07084-516">单例</span><span class="sxs-lookup"><span data-stu-id="07084-516">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="07084-517">单例</span><span class="sxs-lookup"><span data-stu-id="07084-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="07084-518">单例</span><span class="sxs-lookup"><span data-stu-id="07084-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="07084-519">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-519">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="07084-520">单例</span><span class="sxs-lookup"><span data-stu-id="07084-520">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="07084-521">单例</span><span class="sxs-lookup"><span data-stu-id="07084-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="07084-522">单例</span><span class="sxs-lookup"><span data-stu-id="07084-522">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="07084-523">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="07084-523">Register additional services with extension methods</span></span>

<span data-ttu-id="07084-524">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="07084-524">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="07084-525">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="07084-525">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="07084-526">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="07084-526">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="07084-527">服务生存期</span><span class="sxs-lookup"><span data-stu-id="07084-527">Service lifetimes</span></span>

<span data-ttu-id="07084-528">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-528">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="07084-529">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="07084-529">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="07084-530">暂时</span><span class="sxs-lookup"><span data-stu-id="07084-530">Transient</span></span>

<span data-ttu-id="07084-531">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="07084-531">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="07084-532">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-532">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="07084-533">在处理请求的应用中，在请求结束时会释放暂时服务。</span><span class="sxs-lookup"><span data-stu-id="07084-533">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="07084-534">作用域</span><span class="sxs-lookup"><span data-stu-id="07084-534">Scoped</span></span>

<span data-ttu-id="07084-535">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 针对每个客户端请求（连接）创建一次。</span><span class="sxs-lookup"><span data-stu-id="07084-535">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="07084-536">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-536">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="07084-537">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="07084-537">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="07084-538">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="07084-538">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="07084-539">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="07084-539">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="07084-540">单例</span><span class="sxs-lookup"><span data-stu-id="07084-540">Singleton</span></span>

<span data-ttu-id="07084-541">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="07084-541">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="07084-542">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="07084-542">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="07084-543">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-543">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="07084-544">不要在类中实现单一实例设计模式并提供用户代码来管理对象的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-544">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="07084-545">在处理请求的应用中，当应用关闭并释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="07084-545">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="07084-546">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="07084-546">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="07084-547">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="07084-547">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="07084-548">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="07084-548">Service registration methods</span></span>

<span data-ttu-id="07084-549">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="07084-549">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="07084-550">方法</span><span class="sxs-lookup"><span data-stu-id="07084-550">Method</span></span> | <span data-ttu-id="07084-551">自动</span><span class="sxs-lookup"><span data-stu-id="07084-551">Automatic</span></span><br><span data-ttu-id="07084-552">对象</span><span class="sxs-lookup"><span data-stu-id="07084-552">object</span></span><br><span data-ttu-id="07084-553">释放</span><span class="sxs-lookup"><span data-stu-id="07084-553">disposal</span></span> | <span data-ttu-id="07084-554">多种</span><span class="sxs-lookup"><span data-stu-id="07084-554">Multiple</span></span><br><span data-ttu-id="07084-555">实现</span><span class="sxs-lookup"><span data-stu-id="07084-555">implementations</span></span> | <span data-ttu-id="07084-556">传递参数</span><span class="sxs-lookup"><span data-stu-id="07084-556">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="07084-557">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-557">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="07084-558">是</span><span class="sxs-lookup"><span data-stu-id="07084-558">Yes</span></span> | <span data-ttu-id="07084-559">是</span><span class="sxs-lookup"><span data-stu-id="07084-559">Yes</span></span> | <span data-ttu-id="07084-560">否</span><span class="sxs-lookup"><span data-stu-id="07084-560">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="07084-561">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-561">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="07084-562">是</span><span class="sxs-lookup"><span data-stu-id="07084-562">Yes</span></span> | <span data-ttu-id="07084-563">是</span><span class="sxs-lookup"><span data-stu-id="07084-563">Yes</span></span> | <span data-ttu-id="07084-564">是</span><span class="sxs-lookup"><span data-stu-id="07084-564">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="07084-565">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-565">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="07084-566">是</span><span class="sxs-lookup"><span data-stu-id="07084-566">Yes</span></span> | <span data-ttu-id="07084-567">否</span><span class="sxs-lookup"><span data-stu-id="07084-567">No</span></span> | <span data-ttu-id="07084-568">否</span><span class="sxs-lookup"><span data-stu-id="07084-568">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="07084-569">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-569">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="07084-570">否</span><span class="sxs-lookup"><span data-stu-id="07084-570">No</span></span> | <span data-ttu-id="07084-571">是</span><span class="sxs-lookup"><span data-stu-id="07084-571">Yes</span></span> | <span data-ttu-id="07084-572">是</span><span class="sxs-lookup"><span data-stu-id="07084-572">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="07084-573">示例：</span><span class="sxs-lookup"><span data-stu-id="07084-573">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="07084-574">否</span><span class="sxs-lookup"><span data-stu-id="07084-574">No</span></span> | <span data-ttu-id="07084-575">否</span><span class="sxs-lookup"><span data-stu-id="07084-575">No</span></span> | <span data-ttu-id="07084-576">是</span><span class="sxs-lookup"><span data-stu-id="07084-576">Yes</span></span> |

<span data-ttu-id="07084-577">要详细了解释放类型，请参阅[服务释放](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="07084-577">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="07084-578">多个实现的常见场景是[为了测试而模拟各个类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="07084-578">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="07084-579">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="07084-579">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="07084-580">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="07084-580">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="07084-581">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="07084-581">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="07084-582">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="07084-582">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="07084-583">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="07084-583">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="07084-584">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="07084-584">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="07084-585">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="07084-585">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="07084-586">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="07084-586">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="07084-587">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="07084-587">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="07084-588">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="07084-588">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="07084-589">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="07084-589">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="07084-590">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="07084-590">Constructor injection behavior</span></span>

<span data-ttu-id="07084-591">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="07084-591">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="07084-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="07084-592"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="07084-593">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="07084-593">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="07084-594">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="07084-594">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="07084-595">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="07084-595">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="07084-596">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="07084-596">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="07084-597">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="07084-597">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="07084-598">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="07084-598">Entity Framework contexts</span></span>

<span data-ttu-id="07084-599">通常将实体框架上下文添加到使用[作用域生存期](#service-lifetimes)的服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="07084-599">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="07084-600">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则默认生存期为作用域。</span><span class="sxs-lookup"><span data-stu-id="07084-600">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="07084-601">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="07084-601">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="07084-602">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="07084-602">Lifetime and registration options</span></span>

<span data-ttu-id="07084-603">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="07084-603">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="07084-604">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="07084-604">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="07084-605">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="07084-605">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="07084-606">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="07084-606">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="07084-607">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="07084-607">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="07084-608">当通过依赖关系注入请求 `OperationService` 时，它将基于依赖服务的生存期接收每个服务的新实例或现有实例。</span><span class="sxs-lookup"><span data-stu-id="07084-608">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="07084-609">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="07084-609">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="07084-610">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="07084-610">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="07084-611">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="07084-611">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="07084-612">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="07084-612">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="07084-613">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="07084-613">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="07084-614">如果创建了一次单例服务和单例实例服务并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="07084-614">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="07084-615">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="07084-615">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="07084-616">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="07084-616">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="07084-617">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="07084-617">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="07084-618">示例应用演示了各个请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-618">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="07084-619">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="07084-619">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="07084-620">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="07084-620">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="07084-621">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="07084-621">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="07084-622">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="07084-622">**First request:**</span></span>

<span data-ttu-id="07084-623">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="07084-623">Controller operations:</span></span>

<span data-ttu-id="07084-624">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="07084-624">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="07084-625">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="07084-625">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="07084-626">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="07084-626">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="07084-627">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="07084-627">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="07084-628">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="07084-628">`OperationService` operations:</span></span>

<span data-ttu-id="07084-629">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="07084-629">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="07084-630">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="07084-630">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="07084-631">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="07084-631">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="07084-632">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="07084-632">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="07084-633">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="07084-633">**Second request:**</span></span>

<span data-ttu-id="07084-634">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="07084-634">Controller operations:</span></span>

<span data-ttu-id="07084-635">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="07084-635">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="07084-636">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="07084-636">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="07084-637">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="07084-637">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="07084-638">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="07084-638">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="07084-639">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="07084-639">`OperationService` operations:</span></span>

<span data-ttu-id="07084-640">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="07084-640">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="07084-641">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="07084-641">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="07084-642">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="07084-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="07084-643">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="07084-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="07084-644">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="07084-644">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="07084-645">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="07084-645">*Transient* objects are always different.</span></span> <span data-ttu-id="07084-646">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="07084-646">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="07084-647">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="07084-647">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="07084-648">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="07084-648">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="07084-649">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="07084-649">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="07084-650">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="07084-650">Call services from main</span></span>

<span data-ttu-id="07084-651">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的作用域服务。 </span><span class="sxs-lookup"><span data-stu-id="07084-651">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="07084-652">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="07084-652">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="07084-653">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="07084-653">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="07084-654">作用域验证</span><span class="sxs-lookup"><span data-stu-id="07084-654">Scope validation</span></span>

<span data-ttu-id="07084-655">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="07084-655">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="07084-656">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-656">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="07084-657">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="07084-657">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="07084-658">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="07084-658">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="07084-659">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="07084-659">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="07084-660">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="07084-660">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="07084-661">如果作用域服务创建于根容器，则该服务的生存期会实际上提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="07084-661">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="07084-662">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="07084-662">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="07084-663">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="07084-663">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="07084-664">请求服务</span><span class="sxs-lookup"><span data-stu-id="07084-664">Request Services</span></span>

<span data-ttu-id="07084-665">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="07084-665">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="07084-666">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-666">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="07084-667">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="07084-667">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="07084-668">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="07084-668">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="07084-669">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="07084-669">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="07084-670">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="07084-670">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="07084-671">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="07084-671">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="07084-672">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="07084-672">Design services for dependency injection</span></span>

<span data-ttu-id="07084-673">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="07084-673">Best practices are to:</span></span>

* <span data-ttu-id="07084-674">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="07084-674">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="07084-675">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="07084-675">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="07084-676">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="07084-676">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="07084-677">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="07084-677">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="07084-678">直接实例化会将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="07084-678">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="07084-679">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="07084-679">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="07084-680">如果一个类似乎有过多的依赖关系注入，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="07084-680">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="07084-681">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="07084-681">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="07084-682">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="07084-682">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="07084-683">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="07084-683">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="07084-684">服务释放</span><span class="sxs-lookup"><span data-stu-id="07084-684">Disposal of services</span></span>

<span data-ttu-id="07084-685">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="07084-685">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="07084-686">如果实例是通过用户代码添加到容器中，则不会自动释放该实例。</span><span class="sxs-lookup"><span data-stu-id="07084-686">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="07084-687">在下面的示例中，服务由服务容器创建，并自动释放：</span><span class="sxs-lookup"><span data-stu-id="07084-687">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="07084-688">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="07084-688">In the following example:</span></span>

* <span data-ttu-id="07084-689">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="07084-689">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="07084-690">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-690">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="07084-691">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="07084-691">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="07084-692">服务如果未通过开发者代码显式释放，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="07084-692">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="07084-693">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="07084-693">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="07084-694">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="07084-694">Transient, limited lifetime</span></span>

<span data-ttu-id="07084-695">**方案**</span><span class="sxs-lookup"><span data-stu-id="07084-695">**Scenario**</span></span>

<span data-ttu-id="07084-696">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="07084-696">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="07084-697">在根作用域内解析实例。</span><span class="sxs-lookup"><span data-stu-id="07084-697">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="07084-698">应在作用域结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="07084-698">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="07084-699">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="07084-699">**Solution**</span></span>

<span data-ttu-id="07084-700">使用工厂模式在父作用域外创建实例。 </span><span class="sxs-lookup"><span data-stu-id="07084-700">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="07084-701">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="07084-701">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="07084-702">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="07084-702">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="07084-703">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="07084-703">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="07084-704">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="07084-704">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="07084-705">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="07084-705">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="07084-706">**方案**</span><span class="sxs-lookup"><span data-stu-id="07084-706">**Scenario**</span></span>

<span data-ttu-id="07084-707">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-707">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="07084-708">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="07084-708">**Solution**</span></span>

<span data-ttu-id="07084-709">为实例注册作用域生存期。 </span><span class="sxs-lookup"><span data-stu-id="07084-709">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="07084-710">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="07084-710">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="07084-711">使用作用域的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="07084-711">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="07084-712">在生存期应结束时释放作用域。</span><span class="sxs-lookup"><span data-stu-id="07084-712">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="07084-713">通用准则</span><span class="sxs-lookup"><span data-stu-id="07084-713">General Guidelines</span></span>

* <span data-ttu-id="07084-714">不要为<xref:System.IDisposable> 实例注册暂时作用域。</span><span class="sxs-lookup"><span data-stu-id="07084-714">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="07084-715">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="07084-715">Use the factory pattern instead.</span></span>
* <span data-ttu-id="07084-716">不要在根作用域内解析暂时或作用域 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="07084-716">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="07084-717">唯一的一般性例外是应用创建/重建和释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="07084-717">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="07084-718">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="07084-718">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="07084-719"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="07084-719">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="07084-720">作用域应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="07084-720">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="07084-721"> 作用域不区分层次，并且在各作用域之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="07084-721">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="07084-722">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="07084-722">Default service container replacement</span></span>

<span data-ttu-id="07084-723">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="07084-723">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="07084-724">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="07084-724">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="07084-725">属性注入</span><span class="sxs-lookup"><span data-stu-id="07084-725">Property injection</span></span>
* <span data-ttu-id="07084-726">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="07084-726">Injection based on name</span></span>
* <span data-ttu-id="07084-727">子容器</span><span class="sxs-lookup"><span data-stu-id="07084-727">Child containers</span></span>
* <span data-ttu-id="07084-728">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="07084-728">Custom lifetime management</span></span>
* <span data-ttu-id="07084-729">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="07084-729">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="07084-730">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="07084-730">Convention-based registration</span></span>

<span data-ttu-id="07084-731">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="07084-731">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="07084-732">Autofac</span><span class="sxs-lookup"><span data-stu-id="07084-732">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="07084-733">DryIoc</span><span class="sxs-lookup"><span data-stu-id="07084-733">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="07084-734">Grace</span><span class="sxs-lookup"><span data-stu-id="07084-734">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="07084-735">LightInject</span><span class="sxs-lookup"><span data-stu-id="07084-735">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="07084-736">Lamar</span><span class="sxs-lookup"><span data-stu-id="07084-736">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="07084-737">Stashbox</span><span class="sxs-lookup"><span data-stu-id="07084-737">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="07084-738">Unity</span><span class="sxs-lookup"><span data-stu-id="07084-738">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="07084-739">线程安全</span><span class="sxs-lookup"><span data-stu-id="07084-739">Thread safety</span></span>

<span data-ttu-id="07084-740">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="07084-740">Create thread-safe singleton services.</span></span> <span data-ttu-id="07084-741">如果单例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="07084-741">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="07084-742">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="07084-742">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="07084-743">像类型 (`static`) 构造函数一样，它保证单个线程只调用一次。</span><span class="sxs-lookup"><span data-stu-id="07084-743">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="07084-744">建议</span><span class="sxs-lookup"><span data-stu-id="07084-744">Recommendations</span></span>

* <span data-ttu-id="07084-745">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="07084-745">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="07084-746">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="07084-746">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="07084-747">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="07084-747">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="07084-748">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="07084-748">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="07084-749">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="07084-749">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="07084-750">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="07084-750">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="07084-751">最好通过 DI 请求实际项。</span><span class="sxs-lookup"><span data-stu-id="07084-751">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="07084-752">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="07084-752">Avoid static access to services.</span></span> <span data-ttu-id="07084-753">例如，避免静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="07084-753">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="07084-754">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="07084-754">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="07084-755">可以使用 DI 代替时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="07084-755">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="07084-756">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="07084-756">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="07084-757">**正确**：</span><span class="sxs-lookup"><span data-stu-id="07084-757">**Correct**:</span></span>

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

* <span data-ttu-id="07084-758">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="07084-758">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="07084-759">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="07084-759">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="07084-760">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="07084-760">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="07084-761">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="07084-761">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="07084-762">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="07084-762">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="07084-763">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="07084-763">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07084-764">其他资源</span><span class="sxs-lookup"><span data-stu-id="07084-764">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="07084-765">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="07084-765">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="07084-766">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="07084-766">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="07084-767">显式依赖关系原则</span><span class="sxs-lookup"><span data-stu-id="07084-767">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="07084-768">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="07084-768">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="07084-769">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="07084-769">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
