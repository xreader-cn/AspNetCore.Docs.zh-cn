---
title: ASP.NET Core 依赖注入
author: rick-anderson
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- 'ASP.NET Core Identity'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678561"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="3c843-103">ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="3c843-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3c843-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="3c843-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="3c843-105">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="3c843-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="3c843-106">有关 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="3c843-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="3c843-107">若要了解如何在控制台应用中使用依赖注入，请参阅 [.NET 中的依赖注入](/dotnet/core/extensions/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="3c843-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="3c843-108">有关选项的依赖项注入的详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="3c843-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="3c843-109">本主题介绍 ASP.NET Core 中的依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="3c843-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="3c843-110">若要了解如何在控制台应用中使用依赖关系注入，请参阅 [.NET 中的依赖关系注入](/dotnet/core/extensions/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3c843-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="3c843-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3c843-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="3c843-112">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="3c843-112">Overview of dependency injection</span></span>

<span data-ttu-id="3c843-113">依赖项是指另一个对象所依赖的对象。</span><span class="sxs-lookup"><span data-stu-id="3c843-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="3c843-114">使用其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="3c843-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="3c843-115">类可以创建 `MyDependency` 类的实例，以便利用其 `WriteMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="3c843-116">在以下示例中，`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="3c843-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="3c843-117">该类创建并直接依赖于 `MyDependency` 类。</span><span class="sxs-lookup"><span data-stu-id="3c843-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="3c843-118">代码依赖项（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="3c843-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="3c843-119">要用不同的实现替换 `MyDependency`，必须修改 `IndexModel` 类。</span><span class="sxs-lookup"><span data-stu-id="3c843-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="3c843-120">如果 `MyDependency` 具有依赖项，则必须由 `IndexModel` 类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="3c843-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="3c843-121">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码将分散在整个应用中。</span><span class="sxs-lookup"><span data-stu-id="3c843-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="3c843-122">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="3c843-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="3c843-123">应用需使用模拟或存根 `MyDependency` 类，而该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="3c843-124">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="3c843-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="3c843-125">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="3c843-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="3c843-126">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3c843-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="3c843-127">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="3c843-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="3c843-128">服务通常已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="3c843-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="3c843-129">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="3c843-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="3c843-130">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="3c843-131">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="3c843-132">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="3c843-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="3c843-133">示例应用使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="3c843-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 方法使用范围内生存期（单个请求的生存期）注册服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="3c843-135">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="3c843-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="3c843-136">在示例应用中，请求 `IMyDependency` 服务并用于调用 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="3c843-137">通过使用 DI 模式，表示控制器：</span><span class="sxs-lookup"><span data-stu-id="3c843-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="3c843-138">不使用具体类型 `MyDependency`，仅使用它实现的 `IMyDependency` 接口。</span><span class="sxs-lookup"><span data-stu-id="3c843-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="3c843-139">这样可以轻松地更改控制器使用的实现，而无需修改控制器。</span><span class="sxs-lookup"><span data-stu-id="3c843-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="3c843-140">不创建 `MyDependency` 的实例，这由 DI 容器创建。</span><span class="sxs-lookup"><span data-stu-id="3c843-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="3c843-141">可以通过使用内置日志记录 API 来改善 `IMyDependency` 接口的实现：</span><span class="sxs-lookup"><span data-stu-id="3c843-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="3c843-142">更新的 `ConfigureServices` 方法注册新的 `IMyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="3c843-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="3c843-143">`MyDependency2` 依赖于 <xref:Microsoft.Extensions.Logging.ILogger%601>，并在构造函数中对其进行请求。</span><span class="sxs-lookup"><span data-stu-id="3c843-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="3c843-144">`ILogger<TCategoryName>` 是[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="3c843-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="3c843-145">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="3c843-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="3c843-146">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3c843-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="3c843-147">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="3c843-148">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="3c843-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="3c843-149">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="3c843-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="3c843-150">在依赖项注入术语中，服务：</span><span class="sxs-lookup"><span data-stu-id="3c843-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="3c843-151">通常是向其他对象提供服务的对象，如 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="3c843-152">与 Web 服务无关，尽管服务可能使用 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="3c843-153">框架提供可靠的[日志记录](xref:fundamentals/logging/index)系统。</span><span class="sxs-lookup"><span data-stu-id="3c843-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="3c843-154">编写上述示例中的 `IMyDependency` 实现来演示基本的 DI，而不是来实现日志记录。</span><span class="sxs-lookup"><span data-stu-id="3c843-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="3c843-155">大多数应用都不需要编写记录器。</span><span class="sxs-lookup"><span data-stu-id="3c843-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="3c843-156">下面的代码演示如何使用默认日志记录，这不要求在 `ConfigureServices` 中注册任何服务：</span><span class="sxs-lookup"><span data-stu-id="3c843-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="3c843-157">使用前面的代码时，无需更新 `ConfigureServices`，因为框架提供[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="3c843-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="3c843-158">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-158">Services injected into Startup</span></span>

<span data-ttu-id="3c843-159">服务可以注入 `Startup` 构造函数和 `Startup.Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="3c843-160">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="3c843-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="3c843-161">任何向 DI 容器注册的服务都可以注入 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="3c843-162">有关详细信息，请参阅 <xref:fundamentals/startup> 和[访问 Startup 中的配置](xref:fundamentals/configuration/index#access-configuration-in-startup)。</span><span class="sxs-lookup"><span data-stu-id="3c843-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="3c843-163">使用扩展方法注册服务组</span><span class="sxs-lookup"><span data-stu-id="3c843-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="3c843-164">ASP.NET Core 框架使用一种约定来注册一组相关服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="3c843-165">约定使用单个 `Add{GROUP_NAME}` 扩展方法来注册该框架功能所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="3c843-166">例如，<Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> 扩展方法会注册 MVC 控制器所需的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="3c843-167">下面的代码通过个人用户帐户由 Razor 页面模板生成，并演示如何使用扩展方法 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> 将其他服务添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="3c843-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="3c843-168">服务生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-168">Service lifetimes</span></span>

<span data-ttu-id="3c843-169">可以使用以下任一生存期注册服务：</span><span class="sxs-lookup"><span data-stu-id="3c843-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="3c843-170">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-170">Transient</span></span>
* <span data-ttu-id="3c843-171">作用域</span><span class="sxs-lookup"><span data-stu-id="3c843-171">Scoped</span></span>
* <span data-ttu-id="3c843-172">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-172">Singleton</span></span>

<span data-ttu-id="3c843-173">下列各部分描述了上述每个生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="3c843-174">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="3c843-175">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-175">Transient</span></span>

<span data-ttu-id="3c843-176">暂时生存期服务是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="3c843-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="3c843-177">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="3c843-178">向 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> 注册暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="3c843-179">在处理请求的应用中，在请求结束时会释放暂时服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="3c843-180">作用域</span><span class="sxs-lookup"><span data-stu-id="3c843-180">Scoped</span></span>

<span data-ttu-id="3c843-181">作用域生存期服务针对每个客户端请求（连接）创建一次。</span><span class="sxs-lookup"><span data-stu-id="3c843-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="3c843-182">向 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 注册范围内服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="3c843-183">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="3c843-184">使用 Entity Framework Core 时，默认情况下 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展方法使用范围内生存期来注册 `DbContext` 类型。</span><span class="sxs-lookup"><span data-stu-id="3c843-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="3c843-185">不要从单一实例解析限定范围的服务，并小心不要间接地这样做，例如通过暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="3c843-186">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="3c843-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="3c843-187">可以：</span><span class="sxs-lookup"><span data-stu-id="3c843-187">It's fine to:</span></span>

<span data-ttu-id="3c843-188">从限定范围或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="3c843-189">从其他范围内或暂时性服务解析范围内服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="3c843-190">默认情况下在开发环境中，从具有较长生存期的其他服务解析服务将引发异常。</span><span class="sxs-lookup"><span data-stu-id="3c843-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="3c843-191">有关详细信息，请参阅[作用域验证](#sv)。</span><span class="sxs-lookup"><span data-stu-id="3c843-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="3c843-192">要在中间件中使用范围内服务，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3c843-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="3c843-193">将服务注入中间件的 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="3c843-194">使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)会引发运行时异常，因为它强制使范围内服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="3c843-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="3c843-195">[生存期和注册选项](#lifetime-and-registration-options)部分中的示例演示了 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="3c843-196">使用[基于工厂的中间件](xref:fundamentals/middleware/extensibility)。</span><span class="sxs-lookup"><span data-stu-id="3c843-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="3c843-197">使用此方法注册的中间件按客户端请求（连接）激活，这也使范围内服务可注入中间件的 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="3c843-198">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="3c843-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="3c843-199">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-199">Singleton</span></span>

<span data-ttu-id="3c843-200">创建单例生命周期服务的情况如下：</span><span class="sxs-lookup"><span data-stu-id="3c843-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="3c843-201">在首次请求它们时进行创建；或者</span><span class="sxs-lookup"><span data-stu-id="3c843-201">The first time they're requested.</span></span>
* <span data-ttu-id="3c843-202">在向容器直接提供实现实例时由开发人员进行创建。</span><span class="sxs-lookup"><span data-stu-id="3c843-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="3c843-203">很少用到此方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-203">This approach is rarely needed.</span></span>

<span data-ttu-id="3c843-204">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="3c843-205">如果应用需要单一实例行为，则允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="3c843-206">不要实现单一实例设计模式，或提供代码来释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="3c843-207">服务永远不应由解析容器服务的代码释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="3c843-208">如果类型或工厂注册为单一实例，则容器自动释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="3c843-209">向 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A> 注册单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="3c843-210">单一实例服务必须是线程安全的，并且通常在无状态服务中使用。</span><span class="sxs-lookup"><span data-stu-id="3c843-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="3c843-211">在处理请求的应用中，当应用关闭并释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="3c843-212">由于应用关闭之前不释放内存，因此请考虑单一实例服务的内存使用。</span><span class="sxs-lookup"><span data-stu-id="3c843-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="3c843-213">不要从单一实例解析限定范围的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="3c843-214">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="3c843-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="3c843-215">可以从范围内或暂时性服务解析单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="3c843-216">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="3c843-216">Service registration methods</span></span>

<span data-ttu-id="3c843-217">框架提供了适用于特定场景的服务注册扩展方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="3c843-218">方法</span><span class="sxs-lookup"><span data-stu-id="3c843-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="3c843-219">自动</span><span class="sxs-lookup"><span data-stu-id="3c843-219">Automatic</span></span><br><span data-ttu-id="3c843-220">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="3c843-220">object</span></span><br><span data-ttu-id="3c843-221">释放</span><span class="sxs-lookup"><span data-stu-id="3c843-221">disposal</span></span> | <span data-ttu-id="3c843-222">多种</span><span class="sxs-lookup"><span data-stu-id="3c843-222">Multiple</span></span><br><span data-ttu-id="3c843-223">实现</span><span class="sxs-lookup"><span data-stu-id="3c843-223">implementations</span></span> | <span data-ttu-id="3c843-224">传递参数</span><span class="sxs-lookup"><span data-stu-id="3c843-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="3c843-225">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="3c843-226">是</span><span class="sxs-lookup"><span data-stu-id="3c843-226">Yes</span></span>                             | <span data-ttu-id="3c843-227">是</span><span class="sxs-lookup"><span data-stu-id="3c843-227">Yes</span></span>                         | <span data-ttu-id="3c843-228">否</span><span class="sxs-lookup"><span data-stu-id="3c843-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="3c843-229">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="3c843-230">是</span><span class="sxs-lookup"><span data-stu-id="3c843-230">Yes</span></span>                             | <span data-ttu-id="3c843-231">是</span><span class="sxs-lookup"><span data-stu-id="3c843-231">Yes</span></span>                         | <span data-ttu-id="3c843-232">是</span><span class="sxs-lookup"><span data-stu-id="3c843-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="3c843-233">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="3c843-234">是</span><span class="sxs-lookup"><span data-stu-id="3c843-234">Yes</span></span>                             | <span data-ttu-id="3c843-235">否</span><span class="sxs-lookup"><span data-stu-id="3c843-235">No</span></span>                          | <span data-ttu-id="3c843-236">否</span><span class="sxs-lookup"><span data-stu-id="3c843-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="3c843-237">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="3c843-238">否</span><span class="sxs-lookup"><span data-stu-id="3c843-238">No</span></span>                              | <span data-ttu-id="3c843-239">是</span><span class="sxs-lookup"><span data-stu-id="3c843-239">Yes</span></span>                         | <span data-ttu-id="3c843-240">是</span><span class="sxs-lookup"><span data-stu-id="3c843-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="3c843-241">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="3c843-242">否</span><span class="sxs-lookup"><span data-stu-id="3c843-242">No</span></span>                              | <span data-ttu-id="3c843-243">否</span><span class="sxs-lookup"><span data-stu-id="3c843-243">No</span></span>                          | <span data-ttu-id="3c843-244">是</span><span class="sxs-lookup"><span data-stu-id="3c843-244">Yes</span></span>       |

<span data-ttu-id="3c843-245">要详细了解释放类型，请参阅[服务释放](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="3c843-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="3c843-246">在[为测试模拟类型](xref:test/integration-tests#inject-mock-services)时，使用多个实现很常见。</span><span class="sxs-lookup"><span data-stu-id="3c843-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="3c843-247">仅使用实现类型注册服务等效于使用相同的实现和服务类型注册该服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="3c843-248">因此，我们不能使用捕获显式服务类型的方法来注册服务的多个实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="3c843-249">这些方法可以注册服务的多个实例，但它们都具有相同的实现类型。</span><span class="sxs-lookup"><span data-stu-id="3c843-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="3c843-250">上述任何服务注册方法都可用于注册同一服务类型的多个服务实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="3c843-251">下面的示例以 `IMyDependency` 作为服务类型调用 `AddSingleton` 两次。</span><span class="sxs-lookup"><span data-stu-id="3c843-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="3c843-252">第二次对 `AddSingleton` 的调用在解析为 `IMyDependency` 时替代上一次调用，在通过 `IEnumerable<IMyDependency>` 解析多个服务时添加到上一次调用。</span><span class="sxs-lookup"><span data-stu-id="3c843-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="3c843-253">通过 `IEnumerable<{SERVICE}>` 解析服务时，服务按其注册顺序显示。</span><span class="sxs-lookup"><span data-stu-id="3c843-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="3c843-254">框架还提供 `TryAdd{LIFETIME}` 扩展方法，只有当尚未注册某个实现时，才注册该服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="3c843-255">在下面的示例中，对 `AddSingleton` 的调用会将 `MyDependency` 注册为 `IMyDependency`的实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="3c843-256">对 `TryAddSingleton` 的调用没有任何作用，因为 `IMyDependency` 已有一个已注册的实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="3c843-257">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3c843-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="3c843-258">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) 方法仅会在没有同一类型实现的情况下才注册该服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="3c843-259">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="3c843-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="3c843-260">注册服务时，开发人员应在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="3c843-261">通常库作者使用 `TryAddEnumerable` 来避免在容器中注册实现的多个副本。</span><span class="sxs-lookup"><span data-stu-id="3c843-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="3c843-262">在下面的示例中，对 `TryAddEnumerable` 的第一次调用会将 `MyDependency` 注册为 `IMyDependency1`的实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="3c843-263">第二次调用向 `IMyDependency2` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="3c843-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="3c843-264">第三次调用没有任何作用，因为 `IMyDependency1` 已有一个 `MyDependency` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="3c843-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="3c843-265">服务注册通常与顺序无关，除了注册同一类型的多个实现时。</span><span class="sxs-lookup"><span data-stu-id="3c843-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="3c843-266">`IServiceCollection` 是 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 对象的集合。</span><span class="sxs-lookup"><span data-stu-id="3c843-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="3c843-267">以下示例演示如何通过创建和添加 `ServiceDescriptor` 来注册服务：</span><span class="sxs-lookup"><span data-stu-id="3c843-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="3c843-268">内置 `Add{LIFETIME}` 方法使用同一种方式。</span><span class="sxs-lookup"><span data-stu-id="3c843-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="3c843-269">相关示例请参阅 [AddScoped 源代码](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)。</span><span class="sxs-lookup"><span data-stu-id="3c843-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="3c843-270">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="3c843-270">Constructor injection behavior</span></span>

<span data-ttu-id="3c843-271">服务可使用以下方式来解析：</span><span class="sxs-lookup"><span data-stu-id="3c843-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="3c843-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="3c843-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="3c843-273">创建未在容器中注册的对象。</span><span class="sxs-lookup"><span data-stu-id="3c843-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="3c843-274">与框架功能一起使用，例如[标记帮助程序](xref:mvc/views/tag-helpers/intro)、[MVC 控制器](xref:mvc/models/model-binding)和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="3c843-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="3c843-275">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="3c843-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="3c843-276">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="3c843-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="3c843-277">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="3c843-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="3c843-278">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="3c843-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="3c843-279">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="3c843-279">Entity Framework contexts</span></span>

<span data-ttu-id="3c843-280">默认情况下，使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="3c843-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="3c843-281">要使用其他生存期，请使用 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 重载来指定生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="3c843-282">给定生存期的服务不应使用生存期比服务生存期短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="3c843-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="3c843-283">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="3c843-283">Lifetime and registration options</span></span>

<span data-ttu-id="3c843-284">为了演示服务生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="3c843-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="3c843-285">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="3c843-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="3c843-286">以下 `Operation` 类实现了前面的所有接口。</span><span class="sxs-lookup"><span data-stu-id="3c843-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="3c843-287">`Operation` 构造函数生成 GUID，并将最后 4 个字符存储在 `OperationId` 属性中：</span><span class="sxs-lookup"><span data-stu-id="3c843-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="3c843-288">`Startup.ConfigureServices` 方法根据命名生存期创建 `Operation` 类的多个注册：</span><span class="sxs-lookup"><span data-stu-id="3c843-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="3c843-289">示例应用一并演示了请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="3c843-290">`IndexModel` 和中间件请求每种 `IOperation` 类型，并记录各自的 `OperationId`：</span><span class="sxs-lookup"><span data-stu-id="3c843-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="3c843-291">与 `IndexModel` 类似，中间件会解析相同的服务：</span><span class="sxs-lookup"><span data-stu-id="3c843-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="3c843-292">范围内服务必须在 `InvokeAsync` 方法中进行解析：</span><span class="sxs-lookup"><span data-stu-id="3c843-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="3c843-293">记录器输出显示：</span><span class="sxs-lookup"><span data-stu-id="3c843-293">The logger output shows:</span></span>

* <span data-ttu-id="3c843-294">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="3c843-294">*Transient* objects are always different.</span></span> <span data-ttu-id="3c843-295">`IndexModel` 和中间件中的临时 `OperationId` 值不同。</span><span class="sxs-lookup"><span data-stu-id="3c843-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="3c843-296">范围内对象对每个请求而言是相同的，但在请求之间不同。</span><span class="sxs-lookup"><span data-stu-id="3c843-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="3c843-297">单一实例对象对于每个请求是相同的。</span><span class="sxs-lookup"><span data-stu-id="3c843-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="3c843-298">若要减少日志记录输出，请在 appsettings.Development.json 文件中设置“Logging:LogLevel:Microsoft:Error”：</span><span class="sxs-lookup"><span data-stu-id="3c843-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="3c843-299">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="3c843-299">Call services from main</span></span>

<span data-ttu-id="3c843-300">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的作用域服务。 </span><span class="sxs-lookup"><span data-stu-id="3c843-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="3c843-301">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="3c843-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="3c843-302">以下示例演示如何访问范围内 `IMyDependency` 服务并在 `Program.Main` 中调用其 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="3c843-303">作用域验证</span><span class="sxs-lookup"><span data-stu-id="3c843-303">Scope validation</span></span>

<span data-ttu-id="3c843-304">如果应用在[开发环境](xref:fundamentals/environments)中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 以生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="3c843-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="3c843-305">没有从根服务提供程序解析到范围内服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="3c843-306">未将范围内服务注入单一实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="3c843-307">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="3c843-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="3c843-308">在启动提供程序和应用时，根服务提供程序的生存期对应于应用的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="3c843-309">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="3c843-310">如果范围内服务创建于根容器，则该服务的生存期实际上提升至单一实例，因为根容器只会在应用关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="3c843-311">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="3c843-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="3c843-312">有关详细信息，请参阅[作用域验证](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="3c843-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="3c843-313">请求服务</span><span class="sxs-lookup"><span data-stu-id="3c843-313">Request Services</span></span>

<span data-ttu-id="3c843-314">ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="3c843-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="3c843-315">从请求内部请求服务时，将从 `RequestServices` 集合解析服务及其依赖项。</span><span class="sxs-lookup"><span data-stu-id="3c843-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="3c843-316">为每个请求创建一个作用域，`RequestServices` 公开作用域服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="3c843-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="3c843-317">只要请求处于活动状态，所有作用域服务都有效。</span><span class="sxs-lookup"><span data-stu-id="3c843-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="3c843-318">与解析 `RequestServices` 集合中的服务相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="3c843-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="3c843-319">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="3c843-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="3c843-320">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-320">Design services for dependency injection</span></span>

<span data-ttu-id="3c843-321">在设计能够进行依赖注入的服务时：</span><span class="sxs-lookup"><span data-stu-id="3c843-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="3c843-322">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="3c843-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="3c843-323">通过将应用设计为改用单一实例服务，避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="3c843-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="3c843-324">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="3c843-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="3c843-325">直接实例化会将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="3c843-326">不在服务中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="3c843-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="3c843-327">如果一个类有过多注入依赖项，这可能表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="3c843-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="3c843-328">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="3c843-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="3c843-329">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="3c843-329">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="3c843-330">服务释放</span><span class="sxs-lookup"><span data-stu-id="3c843-330">Disposal of services</span></span>

<span data-ttu-id="3c843-331">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="3c843-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="3c843-332">从容器中解析的服务绝对不应由开发人员释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="3c843-333">如果类型或工厂注册为单一实例，则容器自动释放单一实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="3c843-334">在下面的示例中，服务由服务容器创建，并自动释放：</span><span class="sxs-lookup"><span data-stu-id="3c843-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3c843-335">每次刷新索引页后，调试控制台显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="3c843-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="3c843-336">不由服务容器创建的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-336">Services not created by the service container</span></span>

<span data-ttu-id="3c843-337">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="3c843-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="3c843-338">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="3c843-338">In the preceding code:</span></span>

* <span data-ttu-id="3c843-339">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="3c843-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="3c843-340">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="3c843-341">开发人员负责释放服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="3c843-342">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="3c843-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="3c843-343">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-343">Transient, limited lifetime</span></span>

<span data-ttu-id="3c843-344">**方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-344">**Scenario**</span></span>

<span data-ttu-id="3c843-345">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="3c843-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="3c843-346">在根范围（根容器）内解析实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="3c843-347">应在作用域结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="3c843-348">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-348">**Solution**</span></span>

<span data-ttu-id="3c843-349">使用工厂模式在父作用域外创建实例。 </span><span class="sxs-lookup"><span data-stu-id="3c843-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="3c843-350">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="3c843-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="3c843-351">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="3c843-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="3c843-352">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="3c843-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="3c843-353">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="3c843-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="3c843-354">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="3c843-355">**方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-355">**Scenario**</span></span>

<span data-ttu-id="3c843-356">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 实例应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="3c843-357">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-357">**Solution**</span></span>

<span data-ttu-id="3c843-358">为实例注册作用域生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="3c843-359">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 创建新 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="3c843-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="3c843-360">使用作用域的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="3c843-361">如果不再需要范围，请将其释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="3c843-362">一般 IDisposable 准则</span><span class="sxs-lookup"><span data-stu-id="3c843-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="3c843-363">不要为 <xref:System.IDisposable> 实例注册暂时性生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="3c843-364">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="3c843-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="3c843-365">不要在根范围内解析具有暂时性或范围内生存期的 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="3c843-366">唯一的例外是应用创建/重新创建并释放 <xref:System.IServiceProvider> 的情况，但这不是理想模式。</span><span class="sxs-lookup"><span data-stu-id="3c843-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="3c843-367">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="3c843-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="3c843-368"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="3c843-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="3c843-369">使用范围控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="3c843-370"> 作用域不区分层次，并且在各作用域之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="3c843-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="3c843-371">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="3c843-371">Default service container replacement</span></span>

<span data-ttu-id="3c843-372">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="3c843-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="3c843-373">我们建议使用内置容器，除非你需要的特定功能不受它支持，例如：</span><span class="sxs-lookup"><span data-stu-id="3c843-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="3c843-374">属性注入</span><span class="sxs-lookup"><span data-stu-id="3c843-374">Property injection</span></span>
* <span data-ttu-id="3c843-375">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="3c843-375">Injection based on name</span></span>
* <span data-ttu-id="3c843-376">子容器</span><span class="sxs-lookup"><span data-stu-id="3c843-376">Child containers</span></span>
* <span data-ttu-id="3c843-377">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="3c843-377">Custom lifetime management</span></span>
* <span data-ttu-id="3c843-378">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="3c843-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="3c843-379">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="3c843-379">Convention-based registration</span></span>

<span data-ttu-id="3c843-380">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="3c843-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="3c843-381">Autofac</span><span class="sxs-lookup"><span data-stu-id="3c843-381">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="3c843-382">DryIoc</span><span class="sxs-lookup"><span data-stu-id="3c843-382">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="3c843-383">Grace</span><span class="sxs-lookup"><span data-stu-id="3c843-383">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="3c843-384">LightInject</span><span class="sxs-lookup"><span data-stu-id="3c843-384">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="3c843-385">Lamar</span><span class="sxs-lookup"><span data-stu-id="3c843-385">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="3c843-386">Stashbox</span><span class="sxs-lookup"><span data-stu-id="3c843-386">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="3c843-387">Unity</span><span class="sxs-lookup"><span data-stu-id="3c843-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="3c843-388">线程安全</span><span class="sxs-lookup"><span data-stu-id="3c843-388">Thread safety</span></span>

<span data-ttu-id="3c843-389">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="3c843-390">如果单一实例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单一实例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="3c843-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="3c843-391">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="3c843-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="3c843-392">像类型 (`static`) 构造函数一样，它保证仅由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="3c843-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="3c843-393">建议</span><span class="sxs-lookup"><span data-stu-id="3c843-393">Recommendations</span></span>

* <span data-ttu-id="3c843-394">不支持基于`async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="3c843-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="3c843-395">由于 C# 不支持异步构造函数，因此请在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="3c843-396">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="3c843-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="3c843-397">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="3c843-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="3c843-398">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="3c843-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="3c843-399">同样，避免“数据持有者”对象，也就是仅仅为实现对另一个对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="3c843-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="3c843-400">最好通过 DI 请求实际项。</span><span class="sxs-lookup"><span data-stu-id="3c843-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="3c843-401">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-401">Avoid static access to services.</span></span> <span data-ttu-id="3c843-402">例如，避免将 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 捕获为静态字段或属性以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="3c843-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="3c843-403">使 DI 工厂保持快速且同步。</span><span class="sxs-lookup"><span data-stu-id="3c843-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="3c843-404">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="3c843-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="3c843-405">例如，可以使用 DI 代替时，不要调用 <xref:System.IServiceProvider.GetService%2A>  来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="3c843-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="3c843-406">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="3c843-406">**Incorrect:**</span></span>

    ![错误代码](dependency-injection/_static/bad.png)

  <span data-ttu-id="3c843-408">**正确** ：</span><span class="sxs-lookup"><span data-stu-id="3c843-408">**Correct** :</span></span>

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
* <span data-ttu-id="3c843-409">要避免的另一个服务定位器变体是注入需在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="3c843-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="3c843-410">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="3c843-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="3c843-411">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="3c843-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="3c843-412">避免在 `ConfigureServices` 中调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>。</span><span class="sxs-lookup"><span data-stu-id="3c843-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="3c843-413">当开发人员想要在 `ConfigureServices` 中解析服务时，通常会调用 `BuildServiceProvider`。</span><span class="sxs-lookup"><span data-stu-id="3c843-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="3c843-414">例如，假设 `LoginPath` 从配置中加载。</span><span class="sxs-lookup"><span data-stu-id="3c843-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="3c843-415">避免采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-415">Avoid the following approach:</span></span>

  ![调用 BuildServiceProvider 的错误代码](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="3c843-417">在上图中，选择 `services.BuildServiceProvider` 下的绿色波浪线将显示以下 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="3c843-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="3c843-418">ASP0000 从应用程序代码调用“BuildServiceProvider”会导致创建单一实例服务的其他副本。</span><span class="sxs-lookup"><span data-stu-id="3c843-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="3c843-419">考虑依赖项注入服务等替代项作为“Configure”的参数。</span><span class="sxs-lookup"><span data-stu-id="3c843-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="3c843-420">调用 `BuildServiceProvider` 会创建第二个容器，该容器可创建残缺的单一实例并导致跨多个容器引用对象图。</span><span class="sxs-lookup"><span data-stu-id="3c843-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="3c843-421">获取 `LoginPath` 的正确方法是使用选项模式对 DI 的内置支持：</span><span class="sxs-lookup"><span data-stu-id="3c843-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="3c843-422">可释放的暂时性服务由容器捕获以进行释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="3c843-423">如果从顶级容器解析，这会变为内存泄漏。</span><span class="sxs-lookup"><span data-stu-id="3c843-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="3c843-424">启用范围验证，确保应用没有捕获范围内服务的单一实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="3c843-425">有关详细信息，请参阅[作用域验证](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="3c843-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="3c843-426">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="3c843-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="3c843-427">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3c843-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="3c843-428">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="3c843-429">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="3c843-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="3c843-430">DI 中适用于多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="3c843-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="3c843-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 是用于在 ASP.NET Core 上构建模块化多租户应用程序的应用程序框架。</span><span class="sxs-lookup"><span data-stu-id="3c843-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="3c843-432">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="3c843-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="3c843-433">请参阅 [Orchard Core 示例](https://github.com/OrchardCMS/OrchardCore.Samples)，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="3c843-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3c843-434">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-434">Framework-provided services</span></span>

<span data-ttu-id="3c843-435">`Startup.ConfigureServices` 方法注册应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="3c843-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="3c843-436">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="3c843-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="3c843-437">对于基于 ASP.NET Core 模板的应用，该框架会注册 250 个以上的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="3c843-438">下表列出了框架注册的这些服务的一小部分：</span><span class="sxs-lookup"><span data-stu-id="3c843-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="3c843-439">服务类型</span><span class="sxs-lookup"><span data-stu-id="3c843-439">Service Type</span></span>                                                                                    | <span data-ttu-id="3c843-440">生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="3c843-441">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="3c843-442">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="3c843-443">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="3c843-444">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="3c843-445">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="3c843-446">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="3c843-447">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="3c843-448">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="3c843-449">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="3c843-450">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="3c843-451">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="3c843-452">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="3c843-453">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="3c843-454">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="3c843-455">其他资源</span><span class="sxs-lookup"><span data-stu-id="3c843-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="3c843-456">用于 DI 应用开发的 NDC 会议模式</span><span class="sxs-lookup"><span data-stu-id="3c843-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="3c843-457">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="3c843-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="3c843-458">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="3c843-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="3c843-459">显式依赖关系原则</span><span class="sxs-lookup"><span data-stu-id="3c843-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="3c843-460">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="3c843-460">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="3c843-461">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-461">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3c843-462">作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="3c843-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="3c843-463">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="3c843-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="3c843-464">有关 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="3c843-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="3c843-465">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3c843-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="3c843-466">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="3c843-466">Overview of dependency injection</span></span>

<span data-ttu-id="3c843-467">依赖项是指另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="3c843-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="3c843-468">查看以下具有 `WriteMessage` 方法的 `MyDependency` 类，此类为应用中其他类所依赖：</span><span class="sxs-lookup"><span data-stu-id="3c843-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="3c843-469">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于其他类。</span><span class="sxs-lookup"><span data-stu-id="3c843-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="3c843-470">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="3c843-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="3c843-471">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="3c843-472">代码依赖关系（如前面的示例）会产生问题，应避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="3c843-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="3c843-473">若要用另一个实现替换 `MyDependency`，则必须修改类。</span><span class="sxs-lookup"><span data-stu-id="3c843-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="3c843-474">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="3c843-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="3c843-475">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码将分散在整个应用中。</span><span class="sxs-lookup"><span data-stu-id="3c843-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="3c843-476">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="3c843-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="3c843-477">应用需使用模拟或存根 `MyDependency` 类，而该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="3c843-478">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="3c843-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="3c843-479">使用接口或基类将依赖关系实现抽象化。</span><span class="sxs-lookup"><span data-stu-id="3c843-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="3c843-480">在服务容器中注册依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3c843-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="3c843-481">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="3c843-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="3c843-482">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="3c843-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="3c843-483">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="3c843-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="3c843-484">框架负责创建依赖关系的实例，并在不再需要时将其释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="3c843-485">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="3c843-486">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="3c843-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="3c843-487">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="3c843-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="3c843-488">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="3c843-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="3c843-489">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3c843-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="3c843-490">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="3c843-491">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="3c843-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="3c843-492">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="3c843-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="3c843-493">`IMyDependency` 在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="3c843-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c843-494">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="3c843-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="3c843-495">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="3c843-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="3c843-496">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="3c843-497">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="3c843-498">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="3c843-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="3c843-499">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="3c843-500">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-500">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="3c843-501">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="3c843-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="3c843-502">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册组。</span><span class="sxs-lookup"><span data-stu-id="3c843-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="3c843-503">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="3c843-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="3c843-504">可通过类的构造函数请求服务实例，在该函数中可使用服务并将其分配给私有字段。</span><span class="sxs-lookup"><span data-stu-id="3c843-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="3c843-505">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="3c843-506">在示例应用中，请求 `IMyDependency` 实例并将其用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="3c843-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="3c843-507">注入 Startup 的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-507">Services injected into Startup</span></span>

<span data-ttu-id="3c843-508">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="3c843-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="3c843-509">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="3c843-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="3c843-510">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="3c843-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3c843-511">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-511">Framework-provided services</span></span>

<span data-ttu-id="3c843-512">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="3c843-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="3c843-513">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="3c843-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="3c843-514">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="3c843-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="3c843-515">下表列出了框架注册的服务的一小部分。</span><span class="sxs-lookup"><span data-stu-id="3c843-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="3c843-516">服务类型</span><span class="sxs-lookup"><span data-stu-id="3c843-516">Service Type</span></span> | <span data-ttu-id="3c843-517">生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="3c843-518">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="3c843-519">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="3c843-520">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="3c843-521">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="3c843-522">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="3c843-523">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="3c843-524">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="3c843-525">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="3c843-526">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="3c843-527">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="3c843-528">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="3c843-529">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="3c843-530">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="3c843-531">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="3c843-532">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="3c843-532">Register additional services with extension methods</span></span>

<span data-ttu-id="3c843-533">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="3c843-534">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="3c843-535">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="3c843-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="3c843-536">服务生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-536">Service lifetimes</span></span>

<span data-ttu-id="3c843-537">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="3c843-538">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="3c843-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="3c843-539">暂时</span><span class="sxs-lookup"><span data-stu-id="3c843-539">Transient</span></span>

<span data-ttu-id="3c843-540">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="3c843-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="3c843-541">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="3c843-542">在处理请求的应用中，在请求结束时会释放暂时服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="3c843-543">作用域</span><span class="sxs-lookup"><span data-stu-id="3c843-543">Scoped</span></span>

<span data-ttu-id="3c843-544">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 针对每个客户端请求（连接）创建一次。</span><span class="sxs-lookup"><span data-stu-id="3c843-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="3c843-545">在处理请求的应用中，在请求结束时会释放有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="3c843-546">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="3c843-547">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="3c843-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="3c843-548">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="3c843-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="3c843-549">单例</span><span class="sxs-lookup"><span data-stu-id="3c843-549">Singleton</span></span>

<span data-ttu-id="3c843-550">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="3c843-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="3c843-551">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="3c843-552">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="3c843-553">不要在类中实现单一实例设计模式并提供用户代码来管理对象的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="3c843-554">在处理请求的应用中，当应用关闭并释放 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 时，会释放单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="3c843-555">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="3c843-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="3c843-556">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="3c843-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="3c843-557">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="3c843-557">Service registration methods</span></span>

<span data-ttu-id="3c843-558">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="3c843-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="3c843-559">方法</span><span class="sxs-lookup"><span data-stu-id="3c843-559">Method</span></span> | <span data-ttu-id="3c843-560">自动</span><span class="sxs-lookup"><span data-stu-id="3c843-560">Automatic</span></span><br><span data-ttu-id="3c843-561">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="3c843-561">object</span></span><br><span data-ttu-id="3c843-562">释放</span><span class="sxs-lookup"><span data-stu-id="3c843-562">disposal</span></span> | <span data-ttu-id="3c843-563">多种</span><span class="sxs-lookup"><span data-stu-id="3c843-563">Multiple</span></span><br><span data-ttu-id="3c843-564">实现</span><span class="sxs-lookup"><span data-stu-id="3c843-564">implementations</span></span> | <span data-ttu-id="3c843-565">传递参数</span><span class="sxs-lookup"><span data-stu-id="3c843-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="3c843-566">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="3c843-567">是</span><span class="sxs-lookup"><span data-stu-id="3c843-567">Yes</span></span> | <span data-ttu-id="3c843-568">是</span><span class="sxs-lookup"><span data-stu-id="3c843-568">Yes</span></span> | <span data-ttu-id="3c843-569">否</span><span class="sxs-lookup"><span data-stu-id="3c843-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="3c843-570">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="3c843-571">是</span><span class="sxs-lookup"><span data-stu-id="3c843-571">Yes</span></span> | <span data-ttu-id="3c843-572">是</span><span class="sxs-lookup"><span data-stu-id="3c843-572">Yes</span></span> | <span data-ttu-id="3c843-573">是</span><span class="sxs-lookup"><span data-stu-id="3c843-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="3c843-574">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="3c843-575">是</span><span class="sxs-lookup"><span data-stu-id="3c843-575">Yes</span></span> | <span data-ttu-id="3c843-576">否</span><span class="sxs-lookup"><span data-stu-id="3c843-576">No</span></span> | <span data-ttu-id="3c843-577">否</span><span class="sxs-lookup"><span data-stu-id="3c843-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="3c843-578">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="3c843-579">否</span><span class="sxs-lookup"><span data-stu-id="3c843-579">No</span></span> | <span data-ttu-id="3c843-580">是</span><span class="sxs-lookup"><span data-stu-id="3c843-580">Yes</span></span> | <span data-ttu-id="3c843-581">是</span><span class="sxs-lookup"><span data-stu-id="3c843-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="3c843-582">示例：</span><span class="sxs-lookup"><span data-stu-id="3c843-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="3c843-583">否</span><span class="sxs-lookup"><span data-stu-id="3c843-583">No</span></span> | <span data-ttu-id="3c843-584">否</span><span class="sxs-lookup"><span data-stu-id="3c843-584">No</span></span> | <span data-ttu-id="3c843-585">是</span><span class="sxs-lookup"><span data-stu-id="3c843-585">Yes</span></span> |

<span data-ttu-id="3c843-586">要详细了解释放类型，请参阅[服务释放](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="3c843-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="3c843-587">多个实现的常见场景是[为了测试而模拟各个类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="3c843-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="3c843-588">仅使用实现类型注册服务等效于使用相同的实现和服务类型注册该服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="3c843-589">因此，我们不能使用捕获显式服务类型的方法来注册服务的多个实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="3c843-590">这些方法可以注册服务的多个实例，但它们都具有相同的实现类型 。</span><span class="sxs-lookup"><span data-stu-id="3c843-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="3c843-591">上述任何服务注册方法都可用于注册同一服务类型的多个服务实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="3c843-592">下面的示例以 `IMyDependency` 作为服务类型调用 `AddSingleton` 两次。</span><span class="sxs-lookup"><span data-stu-id="3c843-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="3c843-593">第二次对 `AddSingleton` 的调用在解析为 `IMyDependency` 时替代上一次调用，在通过 `IEnumerable<IMyDependency>` 解析多个服务时添加到上一次调用。</span><span class="sxs-lookup"><span data-stu-id="3c843-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="3c843-594">通过 `IEnumerable<{SERVICE}>` 解析服务时，服务按其注册顺序显示。</span><span class="sxs-lookup"><span data-stu-id="3c843-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="3c843-595">框架还提供 `TryAdd{LIFETIME}` 扩展方法，只有当尚未注册某个实现时，才注册该服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="3c843-596">在下面的示例中，对 `AddSingleton` 的调用会将 `MyDependency` 注册为 `IMyDependency`的实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="3c843-597">对 `TryAddSingleton` 的调用没有任何作用，因为 `IMyDependency` 已有一个已注册的实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="3c843-598">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3c843-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="3c843-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="3c843-600">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="3c843-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="3c843-601">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="3c843-602">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="3c843-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="3c843-603">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="3c843-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="3c843-604">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="3c843-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="3c843-605">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="3c843-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="3c843-606">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="3c843-606">Constructor injection behavior</span></span>

<span data-ttu-id="3c843-607">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="3c843-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="3c843-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="3c843-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="3c843-609">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="3c843-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="3c843-610">构造函数可以接受非依赖关系注入提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="3c843-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="3c843-611">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="3c843-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="3c843-612">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="3c843-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="3c843-613">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="3c843-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="3c843-614">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="3c843-614">Entity Framework contexts</span></span>

<span data-ttu-id="3c843-615">通常将实体框架上下文添加到使用[作用域生存期](#service-lifetimes)的服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="3c843-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="3c843-616">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则默认生存期为作用域。</span><span class="sxs-lookup"><span data-stu-id="3c843-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="3c843-617">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="3c843-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="3c843-618">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="3c843-618">Lifetime and registration options</span></span>

<span data-ttu-id="3c843-619">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="3c843-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="3c843-620">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="3c843-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="3c843-621">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="3c843-622">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="3c843-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="3c843-623">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="3c843-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="3c843-624">当通过依赖关系注入请求 `OperationService` 时，它将基于依赖服务的生存期接收每个服务的新实例或现有实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="3c843-625">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="3c843-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="3c843-626">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="3c843-627">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="3c843-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="3c843-628">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="3c843-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="3c843-629">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="3c843-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="3c843-630">如果创建了一次单例服务和单例实例服务并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="3c843-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="3c843-631">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="3c843-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="3c843-632">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="3c843-633">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="3c843-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="3c843-634">示例应用演示了各个请求中和请求之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="3c843-635">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="3c843-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="3c843-636">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="3c843-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="3c843-637">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="3c843-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="3c843-638">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="3c843-638">**First request:**</span></span>

<span data-ttu-id="3c843-639">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="3c843-639">Controller operations:</span></span>

<span data-ttu-id="3c843-640">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="3c843-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="3c843-641">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="3c843-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="3c843-642">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3c843-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3c843-643">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3c843-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3c843-644">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="3c843-644">`OperationService` operations:</span></span>

<span data-ttu-id="3c843-645">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="3c843-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="3c843-646">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="3c843-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="3c843-647">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3c843-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3c843-648">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3c843-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3c843-649">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="3c843-649">**Second request:**</span></span>

<span data-ttu-id="3c843-650">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="3c843-650">Controller operations:</span></span>

<span data-ttu-id="3c843-651">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="3c843-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="3c843-652">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="3c843-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="3c843-653">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3c843-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3c843-654">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3c843-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3c843-655">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="3c843-655">`OperationService` operations:</span></span>

<span data-ttu-id="3c843-656">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="3c843-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="3c843-657">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="3c843-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="3c843-658">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="3c843-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="3c843-659">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="3c843-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="3c843-660">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="3c843-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="3c843-661">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="3c843-661">*Transient* objects are always different.</span></span> <span data-ttu-id="3c843-662">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="3c843-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="3c843-663">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="3c843-664">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="3c843-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="3c843-665">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="3c843-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="3c843-666">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="3c843-666">Call services from main</span></span>

<span data-ttu-id="3c843-667">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的作用域服务。 </span><span class="sxs-lookup"><span data-stu-id="3c843-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="3c843-668">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="3c843-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="3c843-669">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="3c843-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="3c843-670">作用域验证</span><span class="sxs-lookup"><span data-stu-id="3c843-670">Scope validation</span></span>

<span data-ttu-id="3c843-671">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="3c843-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="3c843-672">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="3c843-673">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="3c843-674">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="3c843-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="3c843-675">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="3c843-676">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="3c843-677">如果作用域服务创建于根容器，则该服务的生存期会实际上提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="3c843-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="3c843-678">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="3c843-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="3c843-679">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="3c843-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="3c843-680">请求服务</span><span class="sxs-lookup"><span data-stu-id="3c843-680">Request Services</span></span>

<span data-ttu-id="3c843-681">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="3c843-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="3c843-682">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="3c843-683">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="3c843-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="3c843-684">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="3c843-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="3c843-685">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3c843-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="3c843-686">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="3c843-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="3c843-687">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="3c843-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="3c843-688">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-688">Design services for dependency injection</span></span>

<span data-ttu-id="3c843-689">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="3c843-689">Best practices are to:</span></span>

* <span data-ttu-id="3c843-690">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3c843-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="3c843-691">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="3c843-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="3c843-692">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="3c843-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="3c843-693">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="3c843-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="3c843-694">直接实例化会将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="3c843-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="3c843-695">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="3c843-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="3c843-696">如果一个类似乎有过多的依赖关系注入，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="3c843-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="3c843-697">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="3c843-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="3c843-698">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="3c843-698">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="3c843-699">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="3c843-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="3c843-700">服务释放</span><span class="sxs-lookup"><span data-stu-id="3c843-700">Disposal of services</span></span>

<span data-ttu-id="3c843-701">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="3c843-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="3c843-702">如果实例是通过用户代码添加到容器中，则不会自动释放该实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="3c843-703">在下面的示例中，服务由服务容器创建，并自动释放：</span><span class="sxs-lookup"><span data-stu-id="3c843-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="3c843-704">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="3c843-704">In the following example:</span></span>

* <span data-ttu-id="3c843-705">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="3c843-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="3c843-706">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="3c843-707">框架不会自动释放服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="3c843-708">服务如果未通过开发者代码显式释放，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="3c843-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="3c843-709">暂时和共享实例的 IDisposable 指南</span><span class="sxs-lookup"><span data-stu-id="3c843-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="3c843-710">暂时、有限的生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-710">Transient, limited lifetime</span></span>

<span data-ttu-id="3c843-711">**方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-711">**Scenario**</span></span>

<span data-ttu-id="3c843-712">应用需要一个 <xref:System.IDisposable> 实例，该实例在以下任一情况下具有暂时性生存期：</span><span class="sxs-lookup"><span data-stu-id="3c843-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="3c843-713">在根作用域内解析实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="3c843-714">应在作用域结束之前释放实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="3c843-715">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-715">**Solution**</span></span>

<span data-ttu-id="3c843-716">使用工厂模式在父作用域外创建实例。 </span><span class="sxs-lookup"><span data-stu-id="3c843-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="3c843-717">在这种情况下，应用通常会使用一个 `Create` 方法，该方法直接调用最终类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="3c843-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="3c843-718">如果最终类型具有其他依赖项，则工厂可以：</span><span class="sxs-lookup"><span data-stu-id="3c843-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="3c843-719">在其构造函数中接收 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="3c843-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="3c843-720">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 在容器外部实例化实例，同时将容器用于其依赖项。</span><span class="sxs-lookup"><span data-stu-id="3c843-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="3c843-721">共享实例，有限的生存期</span><span class="sxs-lookup"><span data-stu-id="3c843-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="3c843-722">**方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-722">**Scenario**</span></span>

<span data-ttu-id="3c843-723">应用需要跨多个服务的共享 <xref:System.IDisposable> 实例，但 <xref:System.IDisposable> 应具有有限的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="3c843-724">**解决方案**</span><span class="sxs-lookup"><span data-stu-id="3c843-724">**Solution**</span></span>

<span data-ttu-id="3c843-725">为实例注册作用域生存期。 </span><span class="sxs-lookup"><span data-stu-id="3c843-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="3c843-726">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 启动并创建新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>。</span><span class="sxs-lookup"><span data-stu-id="3c843-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="3c843-727">使用作用域的 <xref:System.IServiceProvider> 获取所需的服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="3c843-728">在生存期应结束时释放作用域。</span><span class="sxs-lookup"><span data-stu-id="3c843-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="3c843-729">通用准则</span><span class="sxs-lookup"><span data-stu-id="3c843-729">General Guidelines</span></span>

* <span data-ttu-id="3c843-730">不要为<xref:System.IDisposable> 实例注册暂时作用域。</span><span class="sxs-lookup"><span data-stu-id="3c843-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="3c843-731">请改用工厂模式。</span><span class="sxs-lookup"><span data-stu-id="3c843-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="3c843-732">不要在根作用域内解析暂时或作用域 <xref:System.IDisposable> 实例。</span><span class="sxs-lookup"><span data-stu-id="3c843-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="3c843-733">唯一的一般性例外是应用创建/重建和释放 <xref:System.IServiceProvider> 的情况，这不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="3c843-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="3c843-734">通过 DI 接收 <xref:System.IDisposable> 依赖项不要求接收方自行实现 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="3c843-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="3c843-735"><xref:System.IDisposable> 依赖项的接收方不能对该依赖项调用 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="3c843-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="3c843-736">作用域应该用于控制服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="3c843-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="3c843-737"> 作用域不区分层次，并且在各作用域之间没有特定联系。</span><span class="sxs-lookup"><span data-stu-id="3c843-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="3c843-738">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="3c843-738">Default service container replacement</span></span>

<span data-ttu-id="3c843-739">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="3c843-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="3c843-740">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="3c843-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="3c843-741">属性注入</span><span class="sxs-lookup"><span data-stu-id="3c843-741">Property injection</span></span>
* <span data-ttu-id="3c843-742">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="3c843-742">Injection based on name</span></span>
* <span data-ttu-id="3c843-743">子容器</span><span class="sxs-lookup"><span data-stu-id="3c843-743">Child containers</span></span>
* <span data-ttu-id="3c843-744">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="3c843-744">Custom lifetime management</span></span>
* <span data-ttu-id="3c843-745">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="3c843-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="3c843-746">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="3c843-746">Convention-based registration</span></span>

<span data-ttu-id="3c843-747">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="3c843-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="3c843-748">Autofac</span><span class="sxs-lookup"><span data-stu-id="3c843-748">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="3c843-749">DryIoc</span><span class="sxs-lookup"><span data-stu-id="3c843-749">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="3c843-750">Grace</span><span class="sxs-lookup"><span data-stu-id="3c843-750">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="3c843-751">LightInject</span><span class="sxs-lookup"><span data-stu-id="3c843-751">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="3c843-752">Lamar</span><span class="sxs-lookup"><span data-stu-id="3c843-752">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="3c843-753">Stashbox</span><span class="sxs-lookup"><span data-stu-id="3c843-753">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="3c843-754">Unity</span><span class="sxs-lookup"><span data-stu-id="3c843-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="3c843-755">线程安全</span><span class="sxs-lookup"><span data-stu-id="3c843-755">Thread safety</span></span>

<span data-ttu-id="3c843-756">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="3c843-757">如果单例服务依赖于一个暂时服务，那么暂时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="3c843-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="3c843-758">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="3c843-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="3c843-759">像类型 (`static`) 构造函数一样，它保证单个线程只调用一次。</span><span class="sxs-lookup"><span data-stu-id="3c843-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="3c843-760">建议</span><span class="sxs-lookup"><span data-stu-id="3c843-760">Recommendations</span></span>

* <span data-ttu-id="3c843-761">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="3c843-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="3c843-762">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="3c843-763">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="3c843-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="3c843-764">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="3c843-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="3c843-765">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="3c843-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="3c843-766">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="3c843-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="3c843-767">最好通过 DI 请求实际项。</span><span class="sxs-lookup"><span data-stu-id="3c843-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="3c843-768">避免静态访问服务。</span><span class="sxs-lookup"><span data-stu-id="3c843-768">Avoid static access to services.</span></span> <span data-ttu-id="3c843-769">例如，避免静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="3c843-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="3c843-770">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="3c843-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="3c843-771">可以使用 DI 代替时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="3c843-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="3c843-772">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="3c843-772">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="3c843-773">**正确** ：</span><span class="sxs-lookup"><span data-stu-id="3c843-773">**Correct** :</span></span>

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

* <span data-ttu-id="3c843-774">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="3c843-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="3c843-775">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="3c843-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="3c843-776">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="3c843-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="3c843-777">例外情况很少见，主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3c843-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="3c843-778">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="3c843-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="3c843-779">如果将其与静态对象访问混合使用，则可能无法意识到 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="3c843-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c843-780">其他资源</span><span class="sxs-lookup"><span data-stu-id="3c843-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="3c843-781">在 ASP.NET Core 中释放 IDisposable 的四种方式</span><span class="sxs-lookup"><span data-stu-id="3c843-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="3c843-782">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="3c843-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="3c843-783">显式依赖关系原则</span><span class="sxs-lookup"><span data-stu-id="3c843-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="3c843-784">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="3c843-784">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="3c843-785">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="3c843-785">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
