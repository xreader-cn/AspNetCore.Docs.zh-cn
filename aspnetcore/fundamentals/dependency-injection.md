---
<span data-ttu-id="630b6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-102">'Blazor'</span></span>
- <span data-ttu-id="630b6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-103">'Identity'</span></span>
- <span data-ttu-id="630b6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-105">'Razor'</span></span>
- <span data-ttu-id="630b6-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="630b6-107">在 ASP.NET Core 依赖注入</span><span class="sxs-lookup"><span data-stu-id="630b6-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="630b6-108">作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="630b6-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="630b6-109">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="630b6-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="630b6-110">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="630b6-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="630b6-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="630b6-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="630b6-112">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="630b6-112">Overview of dependency injection</span></span>

<span data-ttu-id="630b6-113">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="630b6-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="630b6-114">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="630b6-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="630b6-115">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="630b6-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="630b6-116">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="630b6-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="630b6-117">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="630b6-118">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="630b6-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="630b6-119">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="630b6-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="630b6-120">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="630b6-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="630b6-121">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="630b6-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="630b6-122">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="630b6-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="630b6-123">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="630b6-124">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="630b6-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="630b6-125">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="630b6-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="630b6-126">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="630b6-127">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="630b6-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="630b6-128">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="630b6-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="630b6-129">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="630b6-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="630b6-130">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="630b6-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="630b6-131">在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="630b6-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="630b6-132">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="630b6-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="630b6-133">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="630b6-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="630b6-134">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="630b6-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="630b6-135">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="630b6-136">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="630b6-137">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="630b6-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="630b6-138">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="630b6-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="630b6-139">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="630b6-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="630b6-140">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="630b6-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="630b6-141">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="630b6-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="630b6-142">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="630b6-143">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="630b6-144">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="630b6-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="630b6-145">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="630b6-146">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="630b6-147">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="630b6-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="630b6-148">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="630b6-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="630b6-149">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="630b6-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="630b6-150">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="630b6-151">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="630b6-152">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="630b6-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="630b6-153">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-153">Services injected into Startup</span></span>

<span data-ttu-id="630b6-154">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="630b6-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="630b6-155">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="630b6-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="630b6-156">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="630b6-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="630b6-157">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-157">Framework-provided services</span></span>

<span data-ttu-id="630b6-158">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="630b6-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="630b6-159">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="630b6-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="630b6-160">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="630b6-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="630b6-161">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="630b6-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="630b6-162">服务类型</span><span class="sxs-lookup"><span data-stu-id="630b6-162">Service Type</span></span> | <span data-ttu-id="630b6-163">生存期</span><span class="sxs-lookup"><span data-stu-id="630b6-163">Lifetime</span></span> |
| ---
<span data-ttu-id="630b6-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-165">'Blazor'</span></span>
- <span data-ttu-id="630b6-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-166">'Identity'</span></span>
- <span data-ttu-id="630b6-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-168">'Razor'</span></span>
- <span data-ttu-id="630b6-169">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-171">'Blazor'</span></span>
- <span data-ttu-id="630b6-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-172">'Identity'</span></span>
- <span data-ttu-id="630b6-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-174">'Razor'</span></span>
- <span data-ttu-id="630b6-175">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-177">'Blazor'</span></span>
- <span data-ttu-id="630b6-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-178">'Identity'</span></span>
- <span data-ttu-id="630b6-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-180">'Razor'</span></span>
- <span data-ttu-id="630b6-181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-183">'Blazor'</span></span>
- <span data-ttu-id="630b6-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-184">'Identity'</span></span>
- <span data-ttu-id="630b6-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-186">'Razor'</span></span>
- <span data-ttu-id="630b6-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-187">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-189">'Blazor'</span></span>
- <span data-ttu-id="630b6-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-190">'Identity'</span></span>
- <span data-ttu-id="630b6-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-192">'Razor'</span></span>
- <span data-ttu-id="630b6-193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-195">'Blazor'</span></span>
- <span data-ttu-id="630b6-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-196">'Identity'</span></span>
- <span data-ttu-id="630b6-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-198">'Razor'</span></span>
- <span data-ttu-id="630b6-199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-199">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | 暂时 | | `IHostApplicationLifetime` | 单一实例 | | `IWebHostEnvironment` | 单一实例 | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | 暂时 | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | 暂时 | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | 暂时 | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | 单一实例 | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | 单一实例 | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | 单一实例 |</span><span class="sxs-lookup"><span data-stu-id="630b6-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="630b6-201">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="630b6-201">Register additional services with extension methods</span></span>

<span data-ttu-id="630b6-202">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="630b6-203">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="630b6-204">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="630b6-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="630b6-205">服务生存期</span><span class="sxs-lookup"><span data-stu-id="630b6-205">Service lifetimes</span></span>

<span data-ttu-id="630b6-206">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="630b6-207">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="630b6-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="630b6-208">暂时</span><span class="sxs-lookup"><span data-stu-id="630b6-208">Transient</span></span>

<span data-ttu-id="630b6-209">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="630b6-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="630b6-210">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="630b6-211">范围内</span><span class="sxs-lookup"><span data-stu-id="630b6-211">Scoped</span></span>

<span data-ttu-id="630b6-212">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="630b6-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="630b6-213">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="630b6-214">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="630b6-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="630b6-215">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="630b6-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="630b6-216">单例</span><span class="sxs-lookup"><span data-stu-id="630b6-216">Singleton</span></span>

<span data-ttu-id="630b6-217">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="630b6-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="630b6-218">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="630b6-219">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="630b6-220">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="630b6-221">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="630b6-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="630b6-222">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="630b6-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="630b6-223">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="630b6-223">Service registration methods</span></span>

<span data-ttu-id="630b6-224">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="630b6-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="630b6-225">方法</span><span class="sxs-lookup"><span data-stu-id="630b6-225">Method</span></span> | <span data-ttu-id="630b6-226">自动</span><span class="sxs-lookup"><span data-stu-id="630b6-226">Automatic</span></span><br><span data-ttu-id="630b6-227">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="630b6-227">object</span></span><br><span data-ttu-id="630b6-228">处置</span><span class="sxs-lookup"><span data-stu-id="630b6-228">disposal</span></span> | <span data-ttu-id="630b6-229">多种</span><span class="sxs-lookup"><span data-stu-id="630b6-229">Multiple</span></span><br><span data-ttu-id="630b6-230">实现</span><span class="sxs-lookup"><span data-stu-id="630b6-230">implementations</span></span> | <span data-ttu-id="630b6-231">传递参数</span><span class="sxs-lookup"><span data-stu-id="630b6-231">Pass args</span></span> |
| ---
<span data-ttu-id="630b6-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-233">'Blazor'</span></span>
- <span data-ttu-id="630b6-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-234">'Identity'</span></span>
- <span data-ttu-id="630b6-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-236">'Razor'</span></span>
- <span data-ttu-id="630b6-237">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-237">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-239">'Blazor'</span></span>
- <span data-ttu-id="630b6-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-240">'Identity'</span></span>
- <span data-ttu-id="630b6-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-242">'Razor'</span></span>
- <span data-ttu-id="630b6-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-245">'Blazor'</span></span>
- <span data-ttu-id="630b6-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-246">'Identity'</span></span>
- <span data-ttu-id="630b6-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-248">'Razor'</span></span>
- <span data-ttu-id="630b6-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-251">'Blazor'</span></span>
- <span data-ttu-id="630b6-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-252">'Identity'</span></span>
- <span data-ttu-id="630b6-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-254">'Razor'</span></span>
- <span data-ttu-id="630b6-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-257">'Blazor'</span></span>
- <span data-ttu-id="630b6-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-258">'Identity'</span></span>
- <span data-ttu-id="630b6-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-260">'Razor'</span></span>
- <span data-ttu-id="630b6-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-263">'Blazor'</span></span>
- <span data-ttu-id="630b6-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-264">'Identity'</span></span>
- <span data-ttu-id="630b6-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-266">'Razor'</span></span>
- <span data-ttu-id="630b6-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-269">'Blazor'</span></span>
- <span data-ttu-id="630b6-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-270">'Identity'</span></span>
- <span data-ttu-id="630b6-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-272">'Razor'</span></span>
- <span data-ttu-id="630b6-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-275">'Blazor'</span></span>
- <span data-ttu-id="630b6-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-276">'Identity'</span></span>
- <span data-ttu-id="630b6-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-278">'Razor'</span></span>
- <span data-ttu-id="630b6-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-281">'Blazor'</span></span>
- <span data-ttu-id="630b6-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-282">'Identity'</span></span>
- <span data-ttu-id="630b6-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-284">'Razor'</span></span>
- <span data-ttu-id="630b6-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-287">'Blazor'</span></span>
- <span data-ttu-id="630b6-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-288">'Identity'</span></span>
- <span data-ttu-id="630b6-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-290">'Razor'</span></span>
- <span data-ttu-id="630b6-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-293">'Blazor'</span></span>
- <span data-ttu-id="630b6-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-294">'Identity'</span></span>
- <span data-ttu-id="630b6-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-296">'Razor'</span></span>
- <span data-ttu-id="630b6-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-299">'Blazor'</span></span>
- <span data-ttu-id="630b6-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-300">'Identity'</span></span>
- <span data-ttu-id="630b6-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-302">'Razor'</span></span>
- <span data-ttu-id="630b6-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-305">'Blazor'</span></span>
- <span data-ttu-id="630b6-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-306">'Identity'</span></span>
- <span data-ttu-id="630b6-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-308">'Razor'</span></span>
- <span data-ttu-id="630b6-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-309">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-311">'Blazor'</span></span>
- <span data-ttu-id="630b6-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-312">'Identity'</span></span>
- <span data-ttu-id="630b6-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-314">'Razor'</span></span>
- <span data-ttu-id="630b6-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-317">'Blazor'</span></span>
- <span data-ttu-id="630b6-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-318">'Identity'</span></span>
- <span data-ttu-id="630b6-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-320">'Razor'</span></span>
- <span data-ttu-id="630b6-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-323">'Blazor'</span></span>
- <span data-ttu-id="630b6-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-324">'Identity'</span></span>
- <span data-ttu-id="630b6-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-326">'Razor'</span></span>
- <span data-ttu-id="630b6-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-329">'Blazor'</span></span>
- <span data-ttu-id="630b6-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-330">'Identity'</span></span>
- <span data-ttu-id="630b6-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-332">'Razor'</span></span>
- <span data-ttu-id="630b6-333">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-335">'Blazor'</span></span>
- <span data-ttu-id="630b6-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-336">'Identity'</span></span>
- <span data-ttu-id="630b6-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-338">'Razor'</span></span>
- <span data-ttu-id="630b6-339">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-341">'Blazor'</span></span>
- <span data-ttu-id="630b6-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-342">'Identity'</span></span>
- <span data-ttu-id="630b6-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-344">'Razor'</span></span>
- <span data-ttu-id="630b6-345">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-347">'Blazor'</span></span>
- <span data-ttu-id="630b6-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-348">'Identity'</span></span>
- <span data-ttu-id="630b6-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-350">'Razor'</span></span>
- <span data-ttu-id="630b6-351">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-353">'Blazor'</span></span>
- <span data-ttu-id="630b6-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-354">'Identity'</span></span>
- <span data-ttu-id="630b6-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-356">'Razor'</span></span>
- <span data-ttu-id="630b6-357">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-359">'Blazor'</span></span>
- <span data-ttu-id="630b6-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-360">'Identity'</span></span>
- <span data-ttu-id="630b6-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-362">'Razor'</span></span>
- <span data-ttu-id="630b6-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-365">'Blazor'</span></span>
- <span data-ttu-id="630b6-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-366">'Identity'</span></span>
- <span data-ttu-id="630b6-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-368">'Razor'</span></span>
- <span data-ttu-id="630b6-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-369">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-371">'Blazor'</span></span>
- <span data-ttu-id="630b6-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-372">'Identity'</span></span>
- <span data-ttu-id="630b6-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-374">'Razor'</span></span>
- <span data-ttu-id="630b6-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-375">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="630b6-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="630b6-377">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-377">Example:</span></span><br><span data-ttu-id="630b6-378">`services.AddSingleton<IMyDep, MyDep>();` | 是 | 是 | 否 | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="630b6-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="630b6-379">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="630b6-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 是 | 是 | 是 | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="630b6-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="630b6-381">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-381">Example:</span></span><br><span data-ttu-id="630b6-382">`services.AddSingleton<MyDep>();` | 是 | 否 | 否 | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="630b6-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="630b6-383">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="630b6-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 否 | 是 | 是 | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="630b6-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="630b6-385">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="630b6-386">`services.AddSingleton(new MyDep("A string!"));` | 否 | 否 | 是 |</span><span class="sxs-lookup"><span data-stu-id="630b6-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="630b6-387">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="630b6-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="630b6-388">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="630b6-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="630b6-389">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="630b6-390">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="630b6-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="630b6-391">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="630b6-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="630b6-392">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="630b6-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="630b6-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="630b6-394">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="630b6-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="630b6-395">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="630b6-396">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="630b6-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="630b6-397">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="630b6-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="630b6-398">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="630b6-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="630b6-399">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="630b6-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="630b6-400">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="630b6-400">Constructor injection behavior</span></span>

<span data-ttu-id="630b6-401">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="630b6-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="630b6-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="630b6-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="630b6-403">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="630b6-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="630b6-404">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="630b6-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="630b6-405">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="630b6-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="630b6-406">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="630b6-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="630b6-407">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="630b6-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="630b6-408">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="630b6-408">Entity Framework contexts</span></span>

<span data-ttu-id="630b6-409">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="630b6-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="630b6-410">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="630b6-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="630b6-411">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="630b6-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="630b6-412">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="630b6-412">Lifetime and registration options</span></span>

<span data-ttu-id="630b6-413">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="630b6-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="630b6-414">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="630b6-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="630b6-415">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="630b6-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="630b6-416">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="630b6-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="630b6-417">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="630b6-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="630b6-418">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="630b6-419">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="630b6-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="630b6-420">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="630b6-421">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="630b6-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="630b6-422">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="630b6-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="630b6-423">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="630b6-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="630b6-424">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="630b6-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="630b6-425">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="630b6-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="630b6-426">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="630b6-427">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="630b6-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="630b6-428">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="630b6-429">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="630b6-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="630b6-430">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="630b6-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="630b6-431">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="630b6-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="630b6-432">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="630b6-432">**First request:**</span></span>

<span data-ttu-id="630b6-433">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-433">Controller operations:</span></span>

<span data-ttu-id="630b6-434">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="630b6-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="630b6-435">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="630b6-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="630b6-436">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-437">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-438">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-438">`OperationService` operations:</span></span>

<span data-ttu-id="630b6-439">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="630b6-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="630b6-440">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="630b6-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="630b6-441">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-442">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-443">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="630b6-443">**Second request:**</span></span>

<span data-ttu-id="630b6-444">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-444">Controller operations:</span></span>

<span data-ttu-id="630b6-445">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="630b6-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="630b6-446">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="630b6-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="630b6-447">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-448">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-449">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-449">`OperationService` operations:</span></span>

<span data-ttu-id="630b6-450">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="630b6-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="630b6-451">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="630b6-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="630b6-452">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-453">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-454">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="630b6-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="630b6-455">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="630b6-455">*Transient* objects are always different.</span></span> <span data-ttu-id="630b6-456">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="630b6-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="630b6-457">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="630b6-458">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="630b6-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="630b6-459">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="630b6-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="630b6-460">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="630b6-460">Call services from main</span></span>

<span data-ttu-id="630b6-461">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="630b6-462">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="630b6-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="630b6-463">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="630b6-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="630b6-464">作用域验证</span><span class="sxs-lookup"><span data-stu-id="630b6-464">Scope validation</span></span>

<span data-ttu-id="630b6-465">如果应用正在开发环境中运行，并调用 [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) 生成主机，默认服务提供程序会执行检查，以确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="630b6-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="630b6-466">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="630b6-467">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="630b6-468">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="630b6-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="630b6-469">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="630b6-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="630b6-470">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="630b6-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="630b6-471">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="630b6-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="630b6-472">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="630b6-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="630b6-473">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="630b6-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="630b6-474">请求服务</span><span class="sxs-lookup"><span data-stu-id="630b6-474">Request Services</span></span>

<span data-ttu-id="630b6-475">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="630b6-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="630b6-476">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="630b6-477">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="630b6-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="630b6-478">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="630b6-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="630b6-479">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="630b6-480">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="630b6-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="630b6-481">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="630b6-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="630b6-482">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-482">Design services for dependency injection</span></span>

<span data-ttu-id="630b6-483">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="630b6-483">Best practices are to:</span></span>

* <span data-ttu-id="630b6-484">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="630b6-485">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="630b6-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="630b6-486">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="630b6-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="630b6-487">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="630b6-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="630b6-488">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="630b6-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="630b6-489">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="630b6-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="630b6-490">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="630b6-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="630b6-491">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="630b6-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="630b6-492">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="630b6-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="630b6-493">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="630b6-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="630b6-494">服务处理</span><span class="sxs-lookup"><span data-stu-id="630b6-494">Disposal of services</span></span>

<span data-ttu-id="630b6-495">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="630b6-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="630b6-496">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="630b6-497">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="630b6-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="630b6-498">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="630b6-498">In the following example:</span></span>

* <span data-ttu-id="630b6-499">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="630b6-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="630b6-500">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="630b6-501">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="630b6-502">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="630b6-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="630b6-503">有关服务处理选项的讨论，请参阅 [ASP.NET Core 中处理 IDisposable 的四种方法](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)。</span><span class="sxs-lookup"><span data-stu-id="630b6-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="630b6-504">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="630b6-504">Default service container replacement</span></span>

<span data-ttu-id="630b6-505">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="630b6-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="630b6-506">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="630b6-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="630b6-507">属性注入</span><span class="sxs-lookup"><span data-stu-id="630b6-507">Property injection</span></span>
* <span data-ttu-id="630b6-508">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="630b6-508">Injection based on name</span></span>
* <span data-ttu-id="630b6-509">子容器</span><span class="sxs-lookup"><span data-stu-id="630b6-509">Child containers</span></span>
* <span data-ttu-id="630b6-510">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="630b6-510">Custom lifetime management</span></span>
* <span data-ttu-id="630b6-511">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="630b6-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="630b6-512">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="630b6-512">Convention-based registration</span></span>

<span data-ttu-id="630b6-513">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="630b6-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="630b6-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="630b6-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="630b6-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="630b6-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="630b6-516">Grace</span><span class="sxs-lookup"><span data-stu-id="630b6-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="630b6-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="630b6-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="630b6-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="630b6-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="630b6-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="630b6-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="630b6-520">Unity</span><span class="sxs-lookup"><span data-stu-id="630b6-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="630b6-521">线程安全</span><span class="sxs-lookup"><span data-stu-id="630b6-521">Thread safety</span></span>

<span data-ttu-id="630b6-522">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="630b6-523">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="630b6-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="630b6-524">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="630b6-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="630b6-525">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="630b6-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="630b6-526">建议</span><span class="sxs-lookup"><span data-stu-id="630b6-526">Recommendations</span></span>

* <span data-ttu-id="630b6-527">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="630b6-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="630b6-528">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="630b6-529">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="630b6-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="630b6-530">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="630b6-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="630b6-531">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="630b6-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="630b6-532">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="630b6-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="630b6-533">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="630b6-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="630b6-534">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="630b6-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="630b6-535">避免使用服务定位器模式。</span><span class="sxs-lookup"><span data-stu-id="630b6-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="630b6-536">例如，可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="630b6-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="630b6-537">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="630b6-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="630b6-538">**正确**：</span><span class="sxs-lookup"><span data-stu-id="630b6-538">**Correct**:</span></span>

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

* <span data-ttu-id="630b6-539">要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。</span><span class="sxs-lookup"><span data-stu-id="630b6-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="630b6-540">这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="630b6-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="630b6-541">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="630b6-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="630b6-542">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="630b6-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="630b6-543">例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="630b6-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="630b6-544">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="630b6-545">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="630b6-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="630b6-546">DI 中多租户的推荐模式</span><span class="sxs-lookup"><span data-stu-id="630b6-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="630b6-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租户。</span><span class="sxs-lookup"><span data-stu-id="630b6-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="630b6-548">有关详细信息，请参阅 [Orchard Core 文档](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="630b6-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="630b6-549">请参阅 https://github.com/OrchardCMS/OrchardCore.Samples 上的示例应用，获取有关如何仅使用 Orchard Core Framework 而无需任何 CMS 特定功能来构建模块化和多租户应用的示例。</span><span class="sxs-lookup"><span data-stu-id="630b6-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="630b6-550">其他资源</span><span class="sxs-lookup"><span data-stu-id="630b6-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="630b6-551">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="630b6-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="630b6-552">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="630b6-552">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="630b6-553">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="630b6-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="630b6-554">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="630b6-555">ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。</span><span class="sxs-lookup"><span data-stu-id="630b6-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="630b6-556">有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="630b6-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="630b6-557">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="630b6-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="630b6-558">依赖关系注入概述</span><span class="sxs-lookup"><span data-stu-id="630b6-558">Overview of dependency injection</span></span>

<span data-ttu-id="630b6-559">依赖项是另一个对象所需的任何对象。</span><span class="sxs-lookup"><span data-stu-id="630b6-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="630b6-560">使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：</span><span class="sxs-lookup"><span data-stu-id="630b6-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="630b6-561">可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。</span><span class="sxs-lookup"><span data-stu-id="630b6-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="630b6-562">`MyDependency` 类是 `IndexModel` 类的依赖项：</span><span class="sxs-lookup"><span data-stu-id="630b6-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="630b6-563">该类创建并直接依赖于 `MyDependency` 实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="630b6-564">代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="630b6-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="630b6-565">要用不同的实现替换 `MyDependency`，必须修改类。</span><span class="sxs-lookup"><span data-stu-id="630b6-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="630b6-566">如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="630b6-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="630b6-567">在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。</span><span class="sxs-lookup"><span data-stu-id="630b6-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="630b6-568">这种实现很难进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="630b6-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="630b6-569">应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="630b6-570">依赖关系注入通过以下方式解决了这些问题：</span><span class="sxs-lookup"><span data-stu-id="630b6-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="630b6-571">使用接口或基类抽象化依赖关系实现。</span><span class="sxs-lookup"><span data-stu-id="630b6-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="630b6-572">注册服务容器中的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="630b6-573">ASP.NET Core 提供了一个内置的服务容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="630b6-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="630b6-574">服务已在应用的 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="630b6-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="630b6-575">将服务注入到使用它的类的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="630b6-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="630b6-576">框架负责创建依赖关系的实例，并在不再需要时对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="630b6-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="630b6-577">在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：</span><span class="sxs-lookup"><span data-stu-id="630b6-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="630b6-578">此接口由具体类型 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="630b6-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="630b6-579">`MyDependency` 在其构造函数中请求一个 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="630b6-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="630b6-580">以链式方式使用依赖关系注入并不罕见。</span><span class="sxs-lookup"><span data-stu-id="630b6-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="630b6-581">每个请求的依赖关系相应地请求其自己的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="630b6-582">容器解析图中的依赖关系并返回完全解析的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="630b6-583">必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。</span><span class="sxs-lookup"><span data-stu-id="630b6-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="630b6-584">必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。</span><span class="sxs-lookup"><span data-stu-id="630b6-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="630b6-585">`IMyDependency` 已在 `Startup.ConfigureServices` 中注册。</span><span class="sxs-lookup"><span data-stu-id="630b6-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="630b6-586">`ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="630b6-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="630b6-587">容器通过利用[（泛型）开放类型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)解析 `ILogger<TCategoryName>`，而无需注册每个[（泛型）构造类型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="630b6-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="630b6-588">在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="630b6-589">注册将服务生存期的范围限定为单个请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="630b6-590">本主题后面将介绍[服务生存期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="630b6-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="630b6-591">每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="630b6-592">例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="630b6-593">我们建议应用遵循此约定。</span><span class="sxs-lookup"><span data-stu-id="630b6-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="630b6-594">将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。</span><span class="sxs-lookup"><span data-stu-id="630b6-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="630b6-595">如果服务的构造函数需要[内置类型](/dotnet/csharp/language-reference/keywords/built-in-types-table)（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入该类型：</span><span class="sxs-lookup"><span data-stu-id="630b6-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="630b6-596">通过使用服务并分配给私有字段的类的构造函数请求服务的实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="630b6-597">该字段用于在整个类中根据需要访问服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="630b6-598">在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="630b6-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="630b6-599">注入启动的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-599">Services injected into Startup</span></span>

<span data-ttu-id="630b6-600">使用泛型主机 (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="630b6-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="630b6-601">服务可以注入 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="630b6-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="630b6-602">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="630b6-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="630b6-603">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-603">Framework-provided services</span></span>

<span data-ttu-id="630b6-604">`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。</span><span class="sxs-lookup"><span data-stu-id="630b6-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="630b6-605">最初，提供给 `ConfigureServices` 的 `IServiceCollection` 具有框架定义的服务（具体取决于[主机配置方式](xref:fundamentals/index#host)）。</span><span class="sxs-lookup"><span data-stu-id="630b6-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="630b6-606">基于 ASP.NET Core 模板的应用程序具有框架注册的数百个服务的情况并不少见。</span><span class="sxs-lookup"><span data-stu-id="630b6-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="630b6-607">下表列出了框架注册的服务的一个小示例。</span><span class="sxs-lookup"><span data-stu-id="630b6-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="630b6-608">服务类型</span><span class="sxs-lookup"><span data-stu-id="630b6-608">Service Type</span></span> | <span data-ttu-id="630b6-609">生存期</span><span class="sxs-lookup"><span data-stu-id="630b6-609">Lifetime</span></span> |
| ---
<span data-ttu-id="630b6-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-611">'Blazor'</span></span>
- <span data-ttu-id="630b6-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-612">'Identity'</span></span>
- <span data-ttu-id="630b6-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-614">'Razor'</span></span>
- <span data-ttu-id="630b6-615">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-617">'Blazor'</span></span>
- <span data-ttu-id="630b6-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-618">'Identity'</span></span>
- <span data-ttu-id="630b6-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-620">'Razor'</span></span>
- <span data-ttu-id="630b6-621">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-623">'Blazor'</span></span>
- <span data-ttu-id="630b6-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-624">'Identity'</span></span>
- <span data-ttu-id="630b6-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-626">'Razor'</span></span>
- <span data-ttu-id="630b6-627">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-629">'Blazor'</span></span>
- <span data-ttu-id="630b6-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-630">'Identity'</span></span>
- <span data-ttu-id="630b6-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-632">'Razor'</span></span>
- <span data-ttu-id="630b6-633">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-633">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-635">'Blazor'</span></span>
- <span data-ttu-id="630b6-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-636">'Identity'</span></span>
- <span data-ttu-id="630b6-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-638">'Razor'</span></span>
- <span data-ttu-id="630b6-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-641">'Blazor'</span></span>
- <span data-ttu-id="630b6-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-642">'Identity'</span></span>
- <span data-ttu-id="630b6-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-644">'Razor'</span></span>
- <span data-ttu-id="630b6-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-645">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | 暂时 | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | 暂时 | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | 暂时 | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | 单一实例 | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | 暂时 | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | 单一实例 | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | 单一实例 | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | 单一实例 |</span><span class="sxs-lookup"><span data-stu-id="630b6-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="630b6-647">使用扩展方法注册附加服务</span><span class="sxs-lookup"><span data-stu-id="630b6-647">Register additional services with extension methods</span></span>

<span data-ttu-id="630b6-648">当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="630b6-649">以下代码是如何使用扩展方法 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 和 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> 向容器添加附加服务的示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="630b6-650">有关详细信息，请参阅 API 文档中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 类。</span><span class="sxs-lookup"><span data-stu-id="630b6-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="630b6-651">服务生存期</span><span class="sxs-lookup"><span data-stu-id="630b6-651">Service lifetimes</span></span>

<span data-ttu-id="630b6-652">为每个注册的服务选择适当的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="630b6-653">可以使用以下生存期配置 ASP.NET Core 服务：</span><span class="sxs-lookup"><span data-stu-id="630b6-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="630b6-654">暂时</span><span class="sxs-lookup"><span data-stu-id="630b6-654">Transient</span></span>

<span data-ttu-id="630b6-655">暂时生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 是每次从服务容器进行请求时创建的。</span><span class="sxs-lookup"><span data-stu-id="630b6-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="630b6-656">这种生存期适合轻量级、 无状态的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="630b6-657">范围内</span><span class="sxs-lookup"><span data-stu-id="630b6-657">Scoped</span></span>

<span data-ttu-id="630b6-658">作用域生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 以每个客户端请求（连接）一次的方式创建。</span><span class="sxs-lookup"><span data-stu-id="630b6-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="630b6-659">在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="630b6-660">请不要通过[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)进行注入，因为它会强制服务的行为与单一实例类似。</span><span class="sxs-lookup"><span data-stu-id="630b6-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="630b6-661">有关详细信息，请参阅 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="630b6-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="630b6-662">单例</span><span class="sxs-lookup"><span data-stu-id="630b6-662">Singleton</span></span>

<span data-ttu-id="630b6-663">单一实例生存期服务 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) 是在第一次请求时（或者在运行 `Startup.ConfigureServices` 并且使用服务注册指定实例时）创建的。</span><span class="sxs-lookup"><span data-stu-id="630b6-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="630b6-664">每个后续请求都使用相同的实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="630b6-665">如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="630b6-666">不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="630b6-667">从单一实例解析有作用域的服务很危险。</span><span class="sxs-lookup"><span data-stu-id="630b6-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="630b6-668">当处理后续请求时，它可能会导致服务处于不正确的状态。</span><span class="sxs-lookup"><span data-stu-id="630b6-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="630b6-669">服务注册方法</span><span class="sxs-lookup"><span data-stu-id="630b6-669">Service registration methods</span></span>

<span data-ttu-id="630b6-670">服务注册扩展方法提供适用于特定场景的重载。</span><span class="sxs-lookup"><span data-stu-id="630b6-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="630b6-671">方法</span><span class="sxs-lookup"><span data-stu-id="630b6-671">Method</span></span> | <span data-ttu-id="630b6-672">自动</span><span class="sxs-lookup"><span data-stu-id="630b6-672">Automatic</span></span><br><span data-ttu-id="630b6-673">对象 (object)</span><span class="sxs-lookup"><span data-stu-id="630b6-673">object</span></span><br><span data-ttu-id="630b6-674">处置</span><span class="sxs-lookup"><span data-stu-id="630b6-674">disposal</span></span> | <span data-ttu-id="630b6-675">多种</span><span class="sxs-lookup"><span data-stu-id="630b6-675">Multiple</span></span><br><span data-ttu-id="630b6-676">实现</span><span class="sxs-lookup"><span data-stu-id="630b6-676">implementations</span></span> | <span data-ttu-id="630b6-677">传递参数</span><span class="sxs-lookup"><span data-stu-id="630b6-677">Pass args</span></span> |
| ---
<span data-ttu-id="630b6-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-679">'Blazor'</span></span>
- <span data-ttu-id="630b6-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-680">'Identity'</span></span>
- <span data-ttu-id="630b6-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-682">'Razor'</span></span>
- <span data-ttu-id="630b6-683">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-683">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-685">'Blazor'</span></span>
- <span data-ttu-id="630b6-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-686">'Identity'</span></span>
- <span data-ttu-id="630b6-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-688">'Razor'</span></span>
- <span data-ttu-id="630b6-689">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-691">'Blazor'</span></span>
- <span data-ttu-id="630b6-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-692">'Identity'</span></span>
- <span data-ttu-id="630b6-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-694">'Razor'</span></span>
- <span data-ttu-id="630b6-695">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-697">'Blazor'</span></span>
- <span data-ttu-id="630b6-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-698">'Identity'</span></span>
- <span data-ttu-id="630b6-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-700">'Razor'</span></span>
- <span data-ttu-id="630b6-701">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-703">'Blazor'</span></span>
- <span data-ttu-id="630b6-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-704">'Identity'</span></span>
- <span data-ttu-id="630b6-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-706">'Razor'</span></span>
- <span data-ttu-id="630b6-707">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-709">'Blazor'</span></span>
- <span data-ttu-id="630b6-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-710">'Identity'</span></span>
- <span data-ttu-id="630b6-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-712">'Razor'</span></span>
- <span data-ttu-id="630b6-713">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-715">'Blazor'</span></span>
- <span data-ttu-id="630b6-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-716">'Identity'</span></span>
- <span data-ttu-id="630b6-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-718">'Razor'</span></span>
- <span data-ttu-id="630b6-719">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-721">'Blazor'</span></span>
- <span data-ttu-id="630b6-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-722">'Identity'</span></span>
- <span data-ttu-id="630b6-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-724">'Razor'</span></span>
- <span data-ttu-id="630b6-725">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-727">'Blazor'</span></span>
- <span data-ttu-id="630b6-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-728">'Identity'</span></span>
- <span data-ttu-id="630b6-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-730">'Razor'</span></span>
- <span data-ttu-id="630b6-731">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-733">'Blazor'</span></span>
- <span data-ttu-id="630b6-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-734">'Identity'</span></span>
- <span data-ttu-id="630b6-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-736">'Razor'</span></span>
- <span data-ttu-id="630b6-737">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-739">'Blazor'</span></span>
- <span data-ttu-id="630b6-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-740">'Identity'</span></span>
- <span data-ttu-id="630b6-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-742">'Razor'</span></span>
- <span data-ttu-id="630b6-743">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-745">'Blazor'</span></span>
- <span data-ttu-id="630b6-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-746">'Identity'</span></span>
- <span data-ttu-id="630b6-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-748">'Razor'</span></span>
- <span data-ttu-id="630b6-749">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-751">'Blazor'</span></span>
- <span data-ttu-id="630b6-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-752">'Identity'</span></span>
- <span data-ttu-id="630b6-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-754">'Razor'</span></span>
- <span data-ttu-id="630b6-755">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-755">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-757">'Blazor'</span></span>
- <span data-ttu-id="630b6-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-758">'Identity'</span></span>
- <span data-ttu-id="630b6-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-760">'Razor'</span></span>
- <span data-ttu-id="630b6-761">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-763">'Blazor'</span></span>
- <span data-ttu-id="630b6-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-764">'Identity'</span></span>
- <span data-ttu-id="630b6-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-766">'Razor'</span></span>
- <span data-ttu-id="630b6-767">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-769">'Blazor'</span></span>
- <span data-ttu-id="630b6-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-770">'Identity'</span></span>
- <span data-ttu-id="630b6-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-772">'Razor'</span></span>
- <span data-ttu-id="630b6-773">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-775">'Blazor'</span></span>
- <span data-ttu-id="630b6-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-776">'Identity'</span></span>
- <span data-ttu-id="630b6-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-778">'Razor'</span></span>
- <span data-ttu-id="630b6-779">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-781">'Blazor'</span></span>
- <span data-ttu-id="630b6-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-782">'Identity'</span></span>
- <span data-ttu-id="630b6-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-784">'Razor'</span></span>
- <span data-ttu-id="630b6-785">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-787">'Blazor'</span></span>
- <span data-ttu-id="630b6-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-788">'Identity'</span></span>
- <span data-ttu-id="630b6-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-790">'Razor'</span></span>
- <span data-ttu-id="630b6-791">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-793">'Blazor'</span></span>
- <span data-ttu-id="630b6-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-794">'Identity'</span></span>
- <span data-ttu-id="630b6-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-796">'Razor'</span></span>
- <span data-ttu-id="630b6-797">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-799">'Blazor'</span></span>
- <span data-ttu-id="630b6-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-800">'Identity'</span></span>
- <span data-ttu-id="630b6-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-802">'Razor'</span></span>
- <span data-ttu-id="630b6-803">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-805">'Blazor'</span></span>
- <span data-ttu-id="630b6-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-806">'Identity'</span></span>
- <span data-ttu-id="630b6-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-808">'Razor'</span></span>
- <span data-ttu-id="630b6-809">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="630b6-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-811">'Blazor'</span></span>
- <span data-ttu-id="630b6-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-812">'Identity'</span></span>
- <span data-ttu-id="630b6-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-814">'Razor'</span></span>
- <span data-ttu-id="630b6-815">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-815">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="630b6-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="630b6-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="630b6-817">'Blazor'</span></span>
- <span data-ttu-id="630b6-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="630b6-818">'Identity'</span></span>
- <span data-ttu-id="630b6-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="630b6-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="630b6-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="630b6-820">'Razor'</span></span>
- <span data-ttu-id="630b6-821">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="630b6-821">'SignalR' uid:</span></span> 

<span data-ttu-id="630b6-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="630b6-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="630b6-823">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-823">Example:</span></span><br><span data-ttu-id="630b6-824">`services.AddSingleton<IMyDep, MyDep>();` | 是 | 是 | 否 | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="630b6-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="630b6-825">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="630b6-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 是 | 是 | 是 | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="630b6-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="630b6-827">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-827">Example:</span></span><br><span data-ttu-id="630b6-828">`services.AddSingleton<MyDep>();` | 是 | 否 | 否 | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="630b6-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="630b6-829">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="630b6-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 否 | 是 | 是 | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="630b6-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="630b6-831">示例：</span><span class="sxs-lookup"><span data-stu-id="630b6-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="630b6-832">`services.AddSingleton(new MyDep("A string!"));` | 否 | 否 | 是 |</span><span class="sxs-lookup"><span data-stu-id="630b6-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="630b6-833">要详细了解类型处置，请参阅[服务处置](#disposal-of-services)部分。</span><span class="sxs-lookup"><span data-stu-id="630b6-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="630b6-834">多个实现的常见场景是[为测试模拟类型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="630b6-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="630b6-835">`TryAdd{LIFETIME}` 方法仅当尚未注册实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="630b6-836">在以下示例中，第一行向 `IMyDependency` 注册 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="630b6-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="630b6-837">第二行没有任何作用，因为 `IMyDependency` 已有一个已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="630b6-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="630b6-838">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="630b6-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="630b6-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法仅当没有同一类型的实现时，注册该服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="630b6-840">多个服务通过 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="630b6-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="630b6-841">注册服务时，开发人员只希望在尚未添加一个相同类型时添加实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="630b6-842">通常情况下，库创建者使用此方法来避免在容器中注册一个实例的两个副本。</span><span class="sxs-lookup"><span data-stu-id="630b6-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="630b6-843">在以下示例中，第一行向 `IMyDep1` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="630b6-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="630b6-844">第二行向 `IMyDep2` 注册 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="630b6-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="630b6-845">第三行没有任何作用，因为 `IMyDep1` 已有一个 `MyDep` 的已注册的实现：</span><span class="sxs-lookup"><span data-stu-id="630b6-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="630b6-846">构造函数注入行为</span><span class="sxs-lookup"><span data-stu-id="630b6-846">Constructor injection behavior</span></span>

<span data-ttu-id="630b6-847">服务可以通过两种机制来解析：</span><span class="sxs-lookup"><span data-stu-id="630b6-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="630b6-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允许在依赖关系注入容器中创建没有服务注册的对象。</span><span class="sxs-lookup"><span data-stu-id="630b6-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="630b6-849">`ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="630b6-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="630b6-850">构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。</span><span class="sxs-lookup"><span data-stu-id="630b6-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="630b6-851">当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)需要 public 构造函数。</span><span class="sxs-lookup"><span data-stu-id="630b6-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="630b6-852">当服务由 `ActivatorUtilities` 解析时，[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)要求只存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="630b6-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="630b6-853">支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。</span><span class="sxs-lookup"><span data-stu-id="630b6-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="630b6-854">实体框架上下文</span><span class="sxs-lookup"><span data-stu-id="630b6-854">Entity Framework contexts</span></span>

<span data-ttu-id="630b6-855">通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为客户端请求。</span><span class="sxs-lookup"><span data-stu-id="630b6-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="630b6-856">如果在注册数据库上下文时，[AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 重载未指定生存期，则设置默认生存期范围。</span><span class="sxs-lookup"><span data-stu-id="630b6-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="630b6-857">给定生存期的服务不应使用生存期比服务短的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="630b6-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="630b6-858">生存期和注册选项</span><span class="sxs-lookup"><span data-stu-id="630b6-858">Lifetime and registration options</span></span>

<span data-ttu-id="630b6-859">为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。</span><span class="sxs-lookup"><span data-stu-id="630b6-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="630b6-860">根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：</span><span class="sxs-lookup"><span data-stu-id="630b6-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="630b6-861">接口在 `Operation` 类中实现。</span><span class="sxs-lookup"><span data-stu-id="630b6-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="630b6-862">`Operation` 构造函数将生成一个 GUID（如果未提供）：</span><span class="sxs-lookup"><span data-stu-id="630b6-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="630b6-863">注册 `OperationService` 取决于，每个其他 `Operation` 类型。</span><span class="sxs-lookup"><span data-stu-id="630b6-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="630b6-864">当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="630b6-865">如果从容器请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="630b6-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="630b6-866">`OperationService` 将接收 `IOperationTransient` 类的新实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="630b6-867">新实例将生成一个不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="630b6-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="630b6-868">如果按客户端请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与客户端请求中 `OperationService` 的该 ID 相同。</span><span class="sxs-lookup"><span data-stu-id="630b6-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="630b6-869">在客户端请求中，两个服务共享不同的 `OperationId` 值。</span><span class="sxs-lookup"><span data-stu-id="630b6-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="630b6-870">如果单一数据库和单一实例服务只创建一次并在所有客户端请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。</span><span class="sxs-lookup"><span data-stu-id="630b6-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="630b6-871">在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：</span><span class="sxs-lookup"><span data-stu-id="630b6-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="630b6-872">`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="630b6-873">此类型在使用时很明显（其 GUID 全部为零）。</span><span class="sxs-lookup"><span data-stu-id="630b6-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="630b6-874">示例应用演示了各个请求中和之间的对象生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="630b6-875">示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="630b6-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="630b6-876">然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="630b6-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="630b6-877">以下两个输出显示了两个请求的结果：</span><span class="sxs-lookup"><span data-stu-id="630b6-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="630b6-878">**第一个请求：**</span><span class="sxs-lookup"><span data-stu-id="630b6-878">**First request:**</span></span>

<span data-ttu-id="630b6-879">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-879">Controller operations:</span></span>

<span data-ttu-id="630b6-880">暂时性：d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="630b6-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="630b6-881">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="630b6-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="630b6-882">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-883">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-884">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-884">`OperationService` operations:</span></span>

<span data-ttu-id="630b6-885">暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="630b6-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="630b6-886">作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="630b6-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="630b6-887">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-888">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-889">**第二个请求：**</span><span class="sxs-lookup"><span data-stu-id="630b6-889">**Second request:**</span></span>

<span data-ttu-id="630b6-890">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-890">Controller operations:</span></span>

<span data-ttu-id="630b6-891">暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="630b6-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="630b6-892">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="630b6-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="630b6-893">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-894">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-895">`OperationService` 操作：</span><span class="sxs-lookup"><span data-stu-id="630b6-895">`OperationService` operations:</span></span>

<span data-ttu-id="630b6-896">暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="630b6-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="630b6-897">作用域：31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="630b6-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="630b6-898">单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="630b6-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="630b6-899">实例：00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="630b6-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="630b6-900">观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：</span><span class="sxs-lookup"><span data-stu-id="630b6-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="630b6-901">暂时性对象始终不同。</span><span class="sxs-lookup"><span data-stu-id="630b6-901">*Transient* objects are always different.</span></span> <span data-ttu-id="630b6-902">第一个和第二个客户端请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在客户端请求内都是不同的。</span><span class="sxs-lookup"><span data-stu-id="630b6-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="630b6-903">为每个服务请求和客户端请求提供了一个新实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="630b6-904">作用域对象在一个客户端请求中是相同的，但在多个客户端请求中是不同的。</span><span class="sxs-lookup"><span data-stu-id="630b6-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="630b6-905">单一实例对象对每个对象和每个请求都是相同的（不管 `Startup.ConfigureServices` 中是否提供 `Operation` 实例）。</span><span class="sxs-lookup"><span data-stu-id="630b6-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="630b6-906">从 main 调用服务</span><span class="sxs-lookup"><span data-stu-id="630b6-906">Call services from main</span></span>

<span data-ttu-id="630b6-907">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 以解析应用范围内的已设置范围的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="630b6-908">此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。</span><span class="sxs-lookup"><span data-stu-id="630b6-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="630b6-909">以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：</span><span class="sxs-lookup"><span data-stu-id="630b6-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="630b6-910">作用域验证</span><span class="sxs-lookup"><span data-stu-id="630b6-910">Scope validation</span></span>

<span data-ttu-id="630b6-911">如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：</span><span class="sxs-lookup"><span data-stu-id="630b6-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="630b6-912">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="630b6-913">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="630b6-914">调用 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 时创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="630b6-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="630b6-915">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="630b6-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="630b6-916">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="630b6-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="630b6-917">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="630b6-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="630b6-918">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="630b6-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="630b6-919">有关详细信息，请参阅 <xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="630b6-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="630b6-920">请求服务</span><span class="sxs-lookup"><span data-stu-id="630b6-920">Request Services</span></span>

<span data-ttu-id="630b6-921">来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公开。</span><span class="sxs-lookup"><span data-stu-id="630b6-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="630b6-922">请求服务表示作为应用的一部分配置和请求的服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="630b6-923">当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。</span><span class="sxs-lookup"><span data-stu-id="630b6-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="630b6-924">通常，应用不应直接使用这些属性。</span><span class="sxs-lookup"><span data-stu-id="630b6-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="630b6-925">相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="630b6-926">这样生成的类更易于测试。</span><span class="sxs-lookup"><span data-stu-id="630b6-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="630b6-927">与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。</span><span class="sxs-lookup"><span data-stu-id="630b6-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="630b6-928">设计能够进行依赖关系注入的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-928">Design services for dependency injection</span></span>

<span data-ttu-id="630b6-929">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="630b6-929">Best practices are to:</span></span>

* <span data-ttu-id="630b6-930">设计服务以使用依赖关系注入来获取其依赖关系。</span><span class="sxs-lookup"><span data-stu-id="630b6-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="630b6-931">避免有状态的、静态类和成员。</span><span class="sxs-lookup"><span data-stu-id="630b6-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="630b6-932">将应用设计为改用单一实例服务，可避免创建全局状态。</span><span class="sxs-lookup"><span data-stu-id="630b6-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="630b6-933">避免在服务中直接实例化依赖类。</span><span class="sxs-lookup"><span data-stu-id="630b6-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="630b6-934">直接实例化将代码耦合到特定实现。</span><span class="sxs-lookup"><span data-stu-id="630b6-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="630b6-935">不在应用类中包含过多内容，确保设计规范，并易于测试。</span><span class="sxs-lookup"><span data-stu-id="630b6-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="630b6-936">如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="630b6-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="630b6-937">尝试通过将某些职责移动到一个新类来重构类。</span><span class="sxs-lookup"><span data-stu-id="630b6-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="630b6-938">请记住，Razor Pages 页面模型类和 MVC 控制器类应关注用户界面问题。</span><span class="sxs-lookup"><span data-stu-id="630b6-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="630b6-939">业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。</span><span class="sxs-lookup"><span data-stu-id="630b6-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="630b6-940">服务处理</span><span class="sxs-lookup"><span data-stu-id="630b6-940">Disposal of services</span></span>

<span data-ttu-id="630b6-941">容器为其创建的 <xref:System.IDisposable> 类型调用 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="630b6-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="630b6-942">如果通过用户代码将实例添加到容器中，则不会自动处理该实例。</span><span class="sxs-lookup"><span data-stu-id="630b6-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="630b6-943">在下面的示例中，服务由服务容器创建，并自动处理：</span><span class="sxs-lookup"><span data-stu-id="630b6-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="630b6-944">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="630b6-944">In the following example:</span></span>

* <span data-ttu-id="630b6-945">服务实例不是由服务容器创建的。</span><span class="sxs-lookup"><span data-stu-id="630b6-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="630b6-946">框架不知道预期的服务生存期。</span><span class="sxs-lookup"><span data-stu-id="630b6-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="630b6-947">框架不会自动处理服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="630b6-948">服务如果未通过开发者代码显式处理，则会一直保留，直到应用关闭。</span><span class="sxs-lookup"><span data-stu-id="630b6-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="630b6-949">默认服务容器替换</span><span class="sxs-lookup"><span data-stu-id="630b6-949">Default service container replacement</span></span>

<span data-ttu-id="630b6-950">内置的服务容器旨在满足框架和大多数消费者应用的需求。</span><span class="sxs-lookup"><span data-stu-id="630b6-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="630b6-951">我们建议使用内置容器，除非你需要的特定功能不受内置容器支持，例如：</span><span class="sxs-lookup"><span data-stu-id="630b6-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="630b6-952">属性注入</span><span class="sxs-lookup"><span data-stu-id="630b6-952">Property injection</span></span>
* <span data-ttu-id="630b6-953">基于名称的注入</span><span class="sxs-lookup"><span data-stu-id="630b6-953">Injection based on name</span></span>
* <span data-ttu-id="630b6-954">子容器</span><span class="sxs-lookup"><span data-stu-id="630b6-954">Child containers</span></span>
* <span data-ttu-id="630b6-955">自定义生存期管理</span><span class="sxs-lookup"><span data-stu-id="630b6-955">Custom lifetime management</span></span>
* <span data-ttu-id="630b6-956">对迟缓初始化的 `Func<T>` 支持</span><span class="sxs-lookup"><span data-stu-id="630b6-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="630b6-957">基于约定的注册</span><span class="sxs-lookup"><span data-stu-id="630b6-957">Convention-based registration</span></span>

<span data-ttu-id="630b6-958">以下第三方容器可用于 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="630b6-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="630b6-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="630b6-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="630b6-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="630b6-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="630b6-961">Grace</span><span class="sxs-lookup"><span data-stu-id="630b6-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="630b6-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="630b6-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="630b6-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="630b6-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="630b6-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="630b6-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="630b6-965">Unity</span><span class="sxs-lookup"><span data-stu-id="630b6-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="630b6-966">线程安全</span><span class="sxs-lookup"><span data-stu-id="630b6-966">Thread safety</span></span>

<span data-ttu-id="630b6-967">创建线程安全的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="630b6-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="630b6-968">如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要线程安全，具体取决于单例使用它的方式。</span><span class="sxs-lookup"><span data-stu-id="630b6-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="630b6-969">单个服务的工厂方法（例如 [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*) 的第二个参数）不必是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="630b6-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="630b6-970">像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。</span><span class="sxs-lookup"><span data-stu-id="630b6-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="630b6-971">建议</span><span class="sxs-lookup"><span data-stu-id="630b6-971">Recommendations</span></span>

* <span data-ttu-id="630b6-972">不支持基于 `async/await` 和 `Task` 的服务解析。</span><span class="sxs-lookup"><span data-stu-id="630b6-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="630b6-973">C# 不支持异步构造函数；因此建议模式是在同步解析服务后使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="630b6-974">避免在服务容器中直接存储数据和配置。</span><span class="sxs-lookup"><span data-stu-id="630b6-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="630b6-975">例如，用户的购物车通常不应添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="630b6-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="630b6-976">配置应使用 [选项模型](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="630b6-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="630b6-977">同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。</span><span class="sxs-lookup"><span data-stu-id="630b6-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="630b6-978">最好通过 DI 请求实际项目。</span><span class="sxs-lookup"><span data-stu-id="630b6-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="630b6-979">避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) 以便在其他地方使用）。</span><span class="sxs-lookup"><span data-stu-id="630b6-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="630b6-980">避免使用服务定位器模式，该模式混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="630b6-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="630b6-981">可以改用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例：</span><span class="sxs-lookup"><span data-stu-id="630b6-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="630b6-982">**不正确：**</span><span class="sxs-lookup"><span data-stu-id="630b6-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="630b6-983">**正确**：</span><span class="sxs-lookup"><span data-stu-id="630b6-983">**Correct**:</span></span>

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

  * <span data-ttu-id="630b6-984">避免注入一个使用 <xref:System.IServiceProvider.GetService*> 在运行时解析依赖项的中心。</span><span class="sxs-lookup"><span data-stu-id="630b6-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="630b6-985">避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)）。</span><span class="sxs-lookup"><span data-stu-id="630b6-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="630b6-986">像任何一组建议一样，你可能会遇到需要忽略某建议的情况。</span><span class="sxs-lookup"><span data-stu-id="630b6-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="630b6-987">例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="630b6-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="630b6-988">DI 是静态/全局对象访问模式的替代方法。</span><span class="sxs-lookup"><span data-stu-id="630b6-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="630b6-989">如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。</span><span class="sxs-lookup"><span data-stu-id="630b6-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="630b6-990">其他资源</span><span class="sxs-lookup"><span data-stu-id="630b6-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="630b6-991">在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) </span><span class="sxs-lookup"><span data-stu-id="630b6-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* <span data-ttu-id="630b6-992">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）</span><span class="sxs-lookup"><span data-stu-id="630b6-992">[Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)</span></span>
* [<span data-ttu-id="630b6-993">控制反转容器和依赖关系注入模式 (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="630b6-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="630b6-994">如何在 ASP.NET Core DI 中注册具有多个接口的服务</span><span class="sxs-lookup"><span data-stu-id="630b6-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
