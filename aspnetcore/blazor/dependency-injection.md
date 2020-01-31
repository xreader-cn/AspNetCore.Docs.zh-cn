---
title: ASP.NET Core Blazor 依赖关系注入
author: guardrex
description: 了解 Blazor 应用如何将服务注入组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 859fd484fc00104575f176fa7d3bf752895475a0
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885504"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="ac960-103">ASP.NET Core Blazor 依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="ac960-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="ac960-104">作者： [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="ac960-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ac960-105">Blazor 支持[依赖关系注入（DI）](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="ac960-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ac960-106">应用可以通过将内置服务注入组件来使用这些服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="ac960-107">应用还可以定义和注册自定义服务，并通过 DI 使其在整个应用中可用。</span><span class="sxs-lookup"><span data-stu-id="ac960-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="ac960-108">DI 是一种用于访问在中心位置配置的服务的技术。</span><span class="sxs-lookup"><span data-stu-id="ac960-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="ac960-109">在 Blazor 应用中，这种方法可用于：</span><span class="sxs-lookup"><span data-stu-id="ac960-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="ac960-110">跨多个组件（称为*单独*服务）共享服务类的单个实例。</span><span class="sxs-lookup"><span data-stu-id="ac960-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="ac960-111">使用引用抽象将组件与具体服务类分离。</span><span class="sxs-lookup"><span data-stu-id="ac960-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="ac960-112">例如，请考虑用于访问应用中的数据的接口 `IDataAccess`。</span><span class="sxs-lookup"><span data-stu-id="ac960-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="ac960-113">接口由具体 `DataAccess` 类实现并在应用服务容器中注册为服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="ac960-114">当组件使用 DI 接收 `IDataAccess` 实现时，组件不与具体类型耦合。</span><span class="sxs-lookup"><span data-stu-id="ac960-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="ac960-115">可以交换实现，这可能是单元测试中的模拟实现。</span><span class="sxs-lookup"><span data-stu-id="ac960-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="ac960-116">默认服务</span><span class="sxs-lookup"><span data-stu-id="ac960-116">Default services</span></span>

<span data-ttu-id="ac960-117">默认服务会自动添加到应用的服务集合。</span><span class="sxs-lookup"><span data-stu-id="ac960-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="ac960-118">服务</span><span class="sxs-lookup"><span data-stu-id="ac960-118">Service</span></span> | <span data-ttu-id="ac960-119">生存期</span><span class="sxs-lookup"><span data-stu-id="ac960-119">Lifetime</span></span> | <span data-ttu-id="ac960-120">描述</span><span class="sxs-lookup"><span data-stu-id="ac960-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="ac960-121">单一实例</span><span class="sxs-lookup"><span data-stu-id="ac960-121">Singleton</span></span> | <span data-ttu-id="ac960-122">提供用于发送 HTTP 请求以及从 URI 所标识资源接收 HTTP 响应的方法。</span><span class="sxs-lookup"><span data-stu-id="ac960-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="ac960-123">Blazor WebAssembly 应用程序中 `HttpClient` 的实例使用浏览器在后台处理 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="ac960-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="ac960-124">默认情况下，Blazor 服务器应用不包括配置为服务的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="ac960-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="ac960-125">向 Blazor 服务器应用提供 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="ac960-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="ac960-126">有关更多信息，请参见<xref:blazor/call-web-api>。</span><span class="sxs-lookup"><span data-stu-id="ac960-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="ac960-127">Singleton （Blazor WebAssembly）</span><span class="sxs-lookup"><span data-stu-id="ac960-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="ac960-128">作用域（Blazor 服务器）</span><span class="sxs-lookup"><span data-stu-id="ac960-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="ac960-129">表示在其中调度 JavaScript 调用的 JavaScript 运行时的实例。</span><span class="sxs-lookup"><span data-stu-id="ac960-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="ac960-130">有关更多信息，请参见<xref:blazor/javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="ac960-130">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="ac960-131">Singleton （Blazor WebAssembly）</span><span class="sxs-lookup"><span data-stu-id="ac960-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="ac960-132">作用域（Blazor 服务器）</span><span class="sxs-lookup"><span data-stu-id="ac960-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="ac960-133">包含用于处理 Uri 和导航状态的帮助器。</span><span class="sxs-lookup"><span data-stu-id="ac960-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="ac960-134">有关详细信息，请参阅[URI 和导航状态帮助](xref:blazor/routing#uri-and-navigation-state-helpers)程序。</span><span class="sxs-lookup"><span data-stu-id="ac960-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="ac960-135">自定义服务提供程序不会自动提供表中列出的默认服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="ac960-136">如果使用自定义服务提供程序并且需要表中所示的任何服务，请将所需服务添加到新的服务提供程序中。</span><span class="sxs-lookup"><span data-stu-id="ac960-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="ac960-137">向应用程序添加服务</span><span class="sxs-lookup"><span data-stu-id="ac960-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="ac960-138">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ac960-138">Blazor WebAssembly</span></span>

<span data-ttu-id="ac960-139">在*Program.cs*的 `Main` 方法中配置应用服务集合的服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="ac960-140">在下面的示例中，为 `IMyDependency`注册了 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="ac960-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="ac960-141">构建主机后，在呈现任何组件之前，可以从根 DI 范围访问服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="ac960-142">这可用于在呈现内容之前运行初始化逻辑：</span><span class="sxs-lookup"><span data-stu-id="ac960-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="ac960-143">主机还提供应用的中央配置实例。</span><span class="sxs-lookup"><span data-stu-id="ac960-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="ac960-144">基于前面的示例，天气服务的 URL 将从默认配置源（例如*appsettings*）传递到 `InitializeWeatherAsync`：</span><span class="sxs-lookup"><span data-stu-id="ac960-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="ac960-145">Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="ac960-145">Blazor Server</span></span>

<span data-ttu-id="ac960-146">创建新应用后，请检查 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="ac960-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="ac960-147">向 `ConfigureServices` 方法传递 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，它是服务描述符对象（<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>）的列表。</span><span class="sxs-lookup"><span data-stu-id="ac960-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="ac960-148">通过向服务集合提供服务描述符来添加服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="ac960-149">下面的示例演示了 `IDataAccess` 接口的概念及其具体实现 `DataAccess`：</span><span class="sxs-lookup"><span data-stu-id="ac960-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="ac960-150">服务生存期</span><span class="sxs-lookup"><span data-stu-id="ac960-150">Service lifetime</span></span>

<span data-ttu-id="ac960-151">可以将服务配置为下表中所示的生存期。</span><span class="sxs-lookup"><span data-stu-id="ac960-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="ac960-152">生存期</span><span class="sxs-lookup"><span data-stu-id="ac960-152">Lifetime</span></span> | <span data-ttu-id="ac960-153">描述</span><span class="sxs-lookup"><span data-stu-id="ac960-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="ac960-154"> WebAssembly 应用当前没有 DI 作用域的概念。</span><span class="sxs-lookup"><span data-stu-id="ac960-154"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="ac960-155">`Scoped`注册的服务的行为类似于 `Singleton` 服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="ac960-156">但是，Blazor Server 宿主模型支持 `Scoped` 生存期。</span><span class="sxs-lookup"><span data-stu-id="ac960-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="ac960-157">在 Blazor Server apps 中，作用域内服务注册的范围为*连接*。</span><span class="sxs-lookup"><span data-stu-id="ac960-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="ac960-158">出于此原因，使用作用域内服务的目的是应该作用于当前用户的服务，即使当前目的是在浏览器中运行客户端。</span><span class="sxs-lookup"><span data-stu-id="ac960-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="ac960-159">DI 创建服务的*单个实例*。</span><span class="sxs-lookup"><span data-stu-id="ac960-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="ac960-160">需要 `Singleton` 服务的所有组件均可接收同一服务的实例。</span><span class="sxs-lookup"><span data-stu-id="ac960-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="ac960-161">每当组件从服务容器获取 `Transient` 服务的实例时，它都会接收服务的*新实例*。</span><span class="sxs-lookup"><span data-stu-id="ac960-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="ac960-162">DI 系统基于 ASP.NET Core 中的 DI 系统。</span><span class="sxs-lookup"><span data-stu-id="ac960-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="ac960-163">有关更多信息，请参见<xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="ac960-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="ac960-164">在组件中请求服务</span><span class="sxs-lookup"><span data-stu-id="ac960-164">Request a service in a component</span></span>

<span data-ttu-id="ac960-165">将服务添加到服务集合后，使用[\@插入](xref:mvc/views/razor#inject)Razor 指令将服务注入到组件。</span><span class="sxs-lookup"><span data-stu-id="ac960-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="ac960-166">`@inject` 有两个参数：</span><span class="sxs-lookup"><span data-stu-id="ac960-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="ac960-167">键入要注入的服务的类型 &ndash;。</span><span class="sxs-lookup"><span data-stu-id="ac960-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="ac960-168">属性 &ndash; 接收插入的应用服务的属性的名称。</span><span class="sxs-lookup"><span data-stu-id="ac960-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="ac960-169">属性不需要手动创建。</span><span class="sxs-lookup"><span data-stu-id="ac960-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="ac960-170">编译器将创建属性。</span><span class="sxs-lookup"><span data-stu-id="ac960-170">The compiler creates the property.</span></span>

<span data-ttu-id="ac960-171">有关更多信息，请参见<xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="ac960-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="ac960-172">使用多个 `@inject` 语句注入不同的服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="ac960-173">下面的示例说明如何使用 `@inject`。</span><span class="sxs-lookup"><span data-stu-id="ac960-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="ac960-174">将实现 `Services.IDataAccess` 的服务注入组件的属性 `DataRepository`。</span><span class="sxs-lookup"><span data-stu-id="ac960-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="ac960-175">请注意代码如何只使用 `IDataAccess` 抽象：</span><span class="sxs-lookup"><span data-stu-id="ac960-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="ac960-176">在内部，生成的属性（`DataRepository`）使用 `InjectAttribute` 属性。</span><span class="sxs-lookup"><span data-stu-id="ac960-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="ac960-177">通常不会直接使用此属性。</span><span class="sxs-lookup"><span data-stu-id="ac960-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="ac960-178">如果基类对于组件是必需的，并且插入的属性也是基类所必需的，请手动添加 `InjectAttribute`：</span><span class="sxs-lookup"><span data-stu-id="ac960-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="ac960-179">在派生自基类的组件中，不需要 `@inject` 指令。</span><span class="sxs-lookup"><span data-stu-id="ac960-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="ac960-180">基类的 `InjectAttribute` 是足够的：</span><span class="sxs-lookup"><span data-stu-id="ac960-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="ac960-181">在服务中使用 DI</span><span class="sxs-lookup"><span data-stu-id="ac960-181">Use DI in services</span></span>

<span data-ttu-id="ac960-182">复杂服务可能需要其他服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-182">Complex services might require additional services.</span></span> <span data-ttu-id="ac960-183">在前面的示例中，`DataAccess` 可能需要 `HttpClient` 的默认服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="ac960-184">`@inject` （或 `InjectAttribute`）不能在服务中使用。</span><span class="sxs-lookup"><span data-stu-id="ac960-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="ac960-185">必须改为使用*构造函数注入*。</span><span class="sxs-lookup"><span data-stu-id="ac960-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="ac960-186">通过将参数添加到服务的构造函数中，添加了所需的服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="ac960-187">当 DI 创建服务时，它将在构造函数中识别它所需要的服务，并相应地提供这些服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="ac960-188">构造函数注入的先决条件：</span><span class="sxs-lookup"><span data-stu-id="ac960-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="ac960-189">一个构造函数必须存在，其参数可以全部通过 DI 完成。</span><span class="sxs-lookup"><span data-stu-id="ac960-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="ac960-190">如果指定默认值，则不允许使用 DI 未涵盖的其他参数。</span><span class="sxs-lookup"><span data-stu-id="ac960-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="ac960-191">适用的构造函数必须是*公共*的。</span><span class="sxs-lookup"><span data-stu-id="ac960-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="ac960-192">必须存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="ac960-192">One applicable constructor must exist.</span></span> <span data-ttu-id="ac960-193">如果出现多义性，DI 会引发异常。</span><span class="sxs-lookup"><span data-stu-id="ac960-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="ac960-194">用于管理 DI 作用域的实用工具基组件类</span><span class="sxs-lookup"><span data-stu-id="ac960-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="ac960-195">在 ASP.NET Core 应用中，作用域内服务通常作用于当前请求。</span><span class="sxs-lookup"><span data-stu-id="ac960-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="ac960-196">请求完成后，DI 系统将释放任何作用域内或暂时性的服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="ac960-197">在 Blazor Server apps 中，请求范围将在客户端连接期间持续，这可能会导致暂时性和作用域内服务的运行时间比预期要长得多。</span><span class="sxs-lookup"><span data-stu-id="ac960-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="ac960-198">若要将服务范围限定于组件的生存期，可以使用 `OwningComponentBase` 和 `OwningComponentBase<TService>` 基类。</span><span class="sxs-lookup"><span data-stu-id="ac960-198">To scope services to the lifetime of a component, you can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="ac960-199">这些基类公开 `IServiceProvider` 类型的 `ScopedServices` 属性，该属性可解析范围限制在组件生存期内的服务。</span><span class="sxs-lookup"><span data-stu-id="ac960-199">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="ac960-200">若要创作从 Razor 中的基类继承的组件，请使用 `@inherits` 指令。</span><span class="sxs-lookup"><span data-stu-id="ac960-200">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```razor
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> <span data-ttu-id="ac960-201">使用 `@inject` 或 `InjectAttribute` 注入到组件中的服务不会在组件的作用域中创建，并绑定到请求范围。</span><span class="sxs-lookup"><span data-stu-id="ac960-201">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac960-202">其他资源</span><span class="sxs-lookup"><span data-stu-id="ac960-202">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
