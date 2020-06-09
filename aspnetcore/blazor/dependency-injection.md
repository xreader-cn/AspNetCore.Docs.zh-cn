---
title: "title:'ASP.NET Core Blazor 依赖关系注入' author: guardrex description:“了解 Blazor 应用如何将服务注入组件。”"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date:2020 年 5 月 19 日 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: d5b0747fb0505499197d1751511600bd91fed41d
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271778"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="f45db-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f45db-103">'Blazor'</span></span>

<span data-ttu-id="f45db-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f45db-104">'Identity'</span></span>

<span data-ttu-id="f45db-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f45db-105">'Let's Encrypt'</span></span> <span data-ttu-id="f45db-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f45db-106">'Razor'</span></span> <span data-ttu-id="f45db-107">'SignalR' uid: blazor/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="f45db-107">'SignalR' uid: blazor/dependency-injection</span></span>

<span data-ttu-id="f45db-108">ASP.NET Core Blazor 依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="f45db-108">ASP.NET Core Blazor dependency injection</span></span> <span data-ttu-id="f45db-109">作者：[Rainer Stropek](https://www.timecockpit.com) 和 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="f45db-109">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

* Blazor<span data-ttu-id="f45db-110"> 支持[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f45db-110"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="f45db-111">应用可通过将内置服务注入组件来使用这些服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-111">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="f45db-112">应用还可定义和注册自定义服务，并通过 DI 使其在整个应用中可用。</span><span class="sxs-lookup"><span data-stu-id="f45db-112">Apps can also define and register custom services and make them available throughout the app via DI.</span></span> <span data-ttu-id="f45db-113">DI 是一种技术，它用于访问配置在中心位置的服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-113">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="f45db-114">该技术可在 Blazor 应用中用于以下方面：</span><span class="sxs-lookup"><span data-stu-id="f45db-114">This can be useful in Blazor apps to:</span></span> <span data-ttu-id="f45db-115">跨多个组件共享服务类的单个实例，称为“单一实例”服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-115">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>

## <a name="default-services"></a><span data-ttu-id="f45db-116">通过使用引用抽象将组件与具体服务类分离。</span><span class="sxs-lookup"><span data-stu-id="f45db-116">Decouple components from concrete service classes by using reference abstractions.</span></span>

<span data-ttu-id="f45db-117">例如，请考虑用接口 `IDataAccess` 来访问应用中数据。</span><span class="sxs-lookup"><span data-stu-id="f45db-117">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span>

| <span data-ttu-id="f45db-118">该接口由具体的 `DataAccess` 类实现，并在应用的服务容器中注册为服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-118">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> | <span data-ttu-id="f45db-119">当组件使用 DI 接收 `IDataAccess` 实现时，组件不与具体类型耦合。</span><span class="sxs-lookup"><span data-stu-id="f45db-119">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> | <span data-ttu-id="f45db-120">可交换实现，目的可能是为了单元测试中的模拟实现。</span><span class="sxs-lookup"><span data-stu-id="f45db-120">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="f45db-121">默认服务</span><span class="sxs-lookup"><span data-stu-id="f45db-121">Default services</span></span> | <span data-ttu-id="f45db-122">默认服务会自动添加到应用的服务集合中。</span><span class="sxs-lookup"><span data-stu-id="f45db-122">Default services are automatically added to the app's service collection.</span></span><br><br><span data-ttu-id="f45db-123">服务</span><span class="sxs-lookup"><span data-stu-id="f45db-123">Service</span></span><br><br><span data-ttu-id="f45db-124">生存期</span><span class="sxs-lookup"><span data-stu-id="f45db-124">Lifetime</span></span> <span data-ttu-id="f45db-125">描述</span><span class="sxs-lookup"><span data-stu-id="f45db-125">Description</span></span><br><br><span data-ttu-id="f45db-126">暂时</span><span class="sxs-lookup"><span data-stu-id="f45db-126">Transient</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="f45db-127">提供用于发送 HTTP 请求以及从 URI 标识的资源接收 HTTP 响应的方法。</span><span class="sxs-lookup"><span data-stu-id="f45db-127">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><span data-ttu-id="f45db-128">Blazor WebAssembly 应用中 <xref:System.Net.Http.HttpClient> 的实例使用浏览器在后台处理 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="f45db-128">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span> | <span data-ttu-id="f45db-129">默认情况下，Blazor 服务器应用不包含配置为服务的 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="f45db-129">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="f45db-130">向 Blazor 服务器应用提供 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="f45db-130">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="f45db-131">有关详细信息，请参阅 <xref:blazor/call-web-api>。</span><span class="sxs-lookup"><span data-stu-id="f45db-131">For more information, see <xref:blazor/call-web-api>.</span></span><br><span data-ttu-id="f45db-132">单一实例 (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="f45db-132">Singleton (Blazor WebAssembly)</span></span> | <span data-ttu-id="f45db-133">范围内（Blazor 服务器）</span><span class="sxs-lookup"><span data-stu-id="f45db-133">Scoped (Blazor Server)</span></span> <span data-ttu-id="f45db-134">表示在其中调度 JavaScript 调用的 JavaScript 运行时实例。</span><span class="sxs-lookup"><span data-stu-id="f45db-134">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> |

<span data-ttu-id="f45db-135">有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="f45db-135">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> <span data-ttu-id="f45db-136">单一实例 (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="f45db-136">Singleton (Blazor WebAssembly)</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="f45db-137">范围内（Blazor 服务器）</span><span class="sxs-lookup"><span data-stu-id="f45db-137">Scoped (Blazor Server)</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="f45db-138">包含用于处理 URI 和导航状态的帮助程序。</span><span class="sxs-lookup"><span data-stu-id="f45db-138">Contains helpers for working with URIs and navigation state.</span></span>

<span data-ttu-id="f45db-139">有关详细信息，请参阅 [URI 和导航状态帮助程序](xref:blazor/routing#uri-and-navigation-state-helpers)。</span><span class="sxs-lookup"><span data-stu-id="f45db-139">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> <span data-ttu-id="f45db-140">自定义服务提供程序不会自动提供表中列出的默认服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-140">A custom service provider doesn't automatically provide the default services listed in the table.</span></span>

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

<span data-ttu-id="f45db-141">如果你使用自定义服务提供程序且需要表中所示的任何服务，请将所需服务添加到新的服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="f45db-141">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span> <span data-ttu-id="f45db-142">向应用添加服务</span><span class="sxs-lookup"><span data-stu-id="f45db-142">Add services to an app</span></span>

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

Blazor<span data-ttu-id="f45db-143"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f45db-143"> WebAssembly</span></span> <span data-ttu-id="f45db-144">在 Program.cs 的 `Main` 方法中配置应用服务集合的服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-144">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span>

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

### <a name="blazor-server"></a><span data-ttu-id="f45db-145">在下例中，为 `IMyDependency` 注册了 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="f45db-145">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

<span data-ttu-id="f45db-146">生成主机后，可在呈现任何组件之前从根 DI 范围访问服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-146">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="f45db-147">这对于在呈现内容之前运行初始化逻辑而言很有用：</span><span class="sxs-lookup"><span data-stu-id="f45db-147">This can be useful for running initialization logic before rendering content:</span></span> <span data-ttu-id="f45db-148">主机还会为应用提供中心配置实例。</span><span class="sxs-lookup"><span data-stu-id="f45db-148">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="f45db-149">在上述示例的基础上，天气服务的 URL 将从默认配置源（例如 appsettings.json）传递到 `InitializeWeatherAsync`：</span><span class="sxs-lookup"><span data-stu-id="f45db-149">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Blazor<span data-ttu-id="f45db-150"> 服务器</span><span class="sxs-lookup"><span data-stu-id="f45db-150"> Server</span></span>

<span data-ttu-id="f45db-151">创建新应用后，请检查 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="f45db-151">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

| <span data-ttu-id="f45db-152">向 <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> 方法传递 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，它是服务描述符对象 (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) 的列表。</span><span class="sxs-lookup"><span data-stu-id="f45db-152">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> | <span data-ttu-id="f45db-153">通过向服务集合提供服务描述符来添加服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-153">Services are added by providing service descriptors to the service collection.</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="f45db-154">下面的示例演示了 `IDataAccess` 接口的概念及其具体实现 `DataAccess`：</span><span class="sxs-lookup"><span data-stu-id="f45db-154">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span> <span data-ttu-id="f45db-155">服务生存期</span><span class="sxs-lookup"><span data-stu-id="f45db-155">Service lifetime</span></span> <span data-ttu-id="f45db-156">可使用下表中所示的生存期配置服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-156">Services can be configured with the lifetimes shown in the following table.</span></span> <span data-ttu-id="f45db-157">生存期</span><span class="sxs-lookup"><span data-stu-id="f45db-157">Lifetime</span></span> <span data-ttu-id="f45db-158">描述</span><span class="sxs-lookup"><span data-stu-id="f45db-158">Description</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Blazor<span data-ttu-id="f45db-159"> WebAssembly 应用当前没有 DI 范围的概念。</span><span class="sxs-lookup"><span data-stu-id="f45db-159"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="f45db-160">已注册 `Scoped` 的服务的行为与 `Singleton` 服务类似。</span><span class="sxs-lookup"><span data-stu-id="f45db-160">`Scoped`-registered services behave like `Singleton` services.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="f45db-161">但是，Blazor 服务器托管模型支持 `Scoped` 生存期。</span><span class="sxs-lookup"><span data-stu-id="f45db-161">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> |

<span data-ttu-id="f45db-162">在 Blazor 服务器应用中，Scoped 服务注册的范围为“连接”。</span><span class="sxs-lookup"><span data-stu-id="f45db-162">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="f45db-163">因此，即使当前意图是在浏览器中运行客户端，对于范围应限定为当前用户的服务来说，首选使用 Scoped 服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-163">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="f45db-164">DI 创建服务的单个实例。</span><span class="sxs-lookup"><span data-stu-id="f45db-164">DI creates a *single instance* of the service.</span></span>

<span data-ttu-id="f45db-165">需要 `Singleton` 服务的所有组件都会接收同一服务的实例。</span><span class="sxs-lookup"><span data-stu-id="f45db-165">All components requiring a `Singleton` service receive an instance of the same service.</span></span> <span data-ttu-id="f45db-166">每当组件从服务容器获取 `Transient` 服务的实例时，它都会接收该服务的新实例。</span><span class="sxs-lookup"><span data-stu-id="f45db-166">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span>

* <span data-ttu-id="f45db-167">DI 系统基于 ASP.NET Core 中的 DI 系统。</span><span class="sxs-lookup"><span data-stu-id="f45db-167">The DI system is based on the DI system in ASP.NET Core.</span></span>
* <span data-ttu-id="f45db-168">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="f45db-168">For more information, see <xref:fundamentals/dependency-injection>.</span></span> <span data-ttu-id="f45db-169">在组件中请求服务</span><span class="sxs-lookup"><span data-stu-id="f45db-169">Request a service in a component</span></span> <span data-ttu-id="f45db-170">将服务添加到服务集合后，使用 [\@inject](xref:mvc/views/razor#inject) Razor 指令将服务注入组件。</span><span class="sxs-lookup"><span data-stu-id="f45db-170">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>

<span data-ttu-id="f45db-171">[`@inject`](xref:mvc/views/razor#inject) 有两个参数：</span><span class="sxs-lookup"><span data-stu-id="f45db-171">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

<span data-ttu-id="f45db-172">类型：要注入的服务的类型。</span><span class="sxs-lookup"><span data-stu-id="f45db-172">Type: The type of the service to inject.</span></span>

<span data-ttu-id="f45db-173">属性：接收注入的应用服务的属性的名称。</span><span class="sxs-lookup"><span data-stu-id="f45db-173">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="f45db-174">属性无需手动创建。</span><span class="sxs-lookup"><span data-stu-id="f45db-174">The property doesn't require manual creation.</span></span> <span data-ttu-id="f45db-175">编译器会创建属性。</span><span class="sxs-lookup"><span data-stu-id="f45db-175">The compiler creates the property.</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="f45db-176">有关详细信息，请参阅 <xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="f45db-176">For more information, see <xref:mvc/views/dependency-injection>.</span></span> <span data-ttu-id="f45db-177">使用多个 [`@inject`](xref:mvc/views/razor#inject) 语句来注入不同的服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-177">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span> <span data-ttu-id="f45db-178">下面的示例展示了如何使用 [`@inject`](xref:mvc/views/razor#inject)。</span><span class="sxs-lookup"><span data-stu-id="f45db-178">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="f45db-179">将实现 `Services.IDataAccess` 的服务注入组件的 `DataRepository` 属性中。</span><span class="sxs-lookup"><span data-stu-id="f45db-179">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="f45db-180">请注意代码是如何仅使用 `IDataAccess` 抽象的：</span><span class="sxs-lookup"><span data-stu-id="f45db-180">Note how the code is only using the `IDataAccess` abstraction:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="f45db-181">在内部，生成的属性 (`DataRepository`) 使用 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="f45db-181">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span>

<span data-ttu-id="f45db-182">通常，不直接使用此特性。</span><span class="sxs-lookup"><span data-stu-id="f45db-182">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="f45db-183">如果组件需要基类，并且基类也需要注入的属性，请手动添加 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性：</span><span class="sxs-lookup"><span data-stu-id="f45db-183">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span> <span data-ttu-id="f45db-184">在派生自基类的组件中，不需要 [`@inject`](xref:mvc/views/razor#inject) 指令。</span><span class="sxs-lookup"><span data-stu-id="f45db-184">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="f45db-185">基类的 <xref:Microsoft.AspNetCore.Components.InjectAttribute> 就已足够：</span><span class="sxs-lookup"><span data-stu-id="f45db-185">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span> <span data-ttu-id="f45db-186">在服务中使用 DI</span><span class="sxs-lookup"><span data-stu-id="f45db-186">Use DI in services</span></span> <span data-ttu-id="f45db-187">复杂的服务可能需要其他服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-187">Complex services might require additional services.</span></span>

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

<span data-ttu-id="f45db-188">在前面的示例中，`DataAccess` 可能需要 <xref:System.Net.Http.HttpClient> 默认服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-188">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span>

* <span data-ttu-id="f45db-189">[`@inject`](xref:mvc/views/razor#inject)（或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性）在服务中不可用。</span><span class="sxs-lookup"><span data-stu-id="f45db-189">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="f45db-190">必须改用构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="f45db-190">*Constructor injection* must be used instead.</span></span>
* <span data-ttu-id="f45db-191">通过向服务的构造函数添加参数来添加所需服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-191">Required services are added by adding parameters to the service's constructor.</span></span>
* <span data-ttu-id="f45db-192">当 DI 创建服务时，它会在构造函数中识别其所需的服务，并相应地提供这些服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-192">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="f45db-193">构造函数注入的先决条件：</span><span class="sxs-lookup"><span data-stu-id="f45db-193">Prerequisites for constructor injection:</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="f45db-194">必须存在一个构造函数，其参数可完全通过 DI 实现。</span><span class="sxs-lookup"><span data-stu-id="f45db-194">One constructor must exist whose arguments can all be fulfilled by DI.</span></span>

<span data-ttu-id="f45db-195">如果指定默认值，则允许使用 DI 未涵盖的其他参数。</span><span class="sxs-lookup"><span data-stu-id="f45db-195">Additional parameters not covered by DI are allowed if they specify default values.</span></span> <span data-ttu-id="f45db-196">适用的构造函数必须是公共函数。</span><span class="sxs-lookup"><span data-stu-id="f45db-196">The applicable constructor must be *public*.</span></span> <span data-ttu-id="f45db-197">必须存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="f45db-197">One applicable constructor must exist.</span></span> <span data-ttu-id="f45db-198">如果出现歧义，DI 会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f45db-198">In case of an ambiguity, DI throws an exception.</span></span>

<span data-ttu-id="f45db-199">用于管理 DI 范围的实用工具基组件类</span><span class="sxs-lookup"><span data-stu-id="f45db-199">Utility base component classes to manage a DI scope</span></span> <span data-ttu-id="f45db-200">在 ASP.NET Core 应用中，Scoped 服务的范围通常限定为当前请求。</span><span class="sxs-lookup"><span data-stu-id="f45db-200">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="f45db-201">请求完成后，DI 系统将处置所有 Scoped 或 Transient 服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-201">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="f45db-202">在 Blazor 服务器应用中，请求范围会在客户端连接期间一直持续，这可能导致 Transient 和 Scoped 服务的生存期比预期要长得多。</span><span class="sxs-lookup"><span data-stu-id="f45db-202">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="f45db-203">在 Blazor WebAssembly 应用中，已注册 Scoped 生存期的服务被视为单一实例，因此它们的生存期比典型 ASP.NET Core 应用中的 Scoped 服务要长。</span><span class="sxs-lookup"><span data-stu-id="f45db-203">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

* <span data-ttu-id="f45db-204">限制 Blazor 应用中服务生存期的一种方法是使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 类型。</span><span class="sxs-lookup"><span data-stu-id="f45db-204">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span>
* <span data-ttu-id="f45db-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 的一种抽象类型，它会创建与组件生存期相对应的 DI 范围。</span><span class="sxs-lookup"><span data-stu-id="f45db-205"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span>

<span data-ttu-id="f45db-206">通过使用此范围，可使用具有 Scoped 生存期的 DI 服务，并使其生存期与组件的生存期一样长。</span><span class="sxs-lookup"><span data-stu-id="f45db-206">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span>

* <span data-ttu-id="f45db-207">销毁组件时，也会处置组件的 Scoped 服务提供程序提供的服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-207">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="f45db-208">这对以下服务很有用：</span><span class="sxs-lookup"><span data-stu-id="f45db-208">This can be useful for services that:</span></span>

  <span data-ttu-id="f45db-209">由于 Transient 生存期不适用而应在组件中重复使用的服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-209">Should be reused within a component, as the transient lifetime is inappropriate.</span></span> <span data-ttu-id="f45db-210">由于 Singleton 生存期不适用而不得跨组件共享的服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-210">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span> <span data-ttu-id="f45db-211">可使用下面两个版本的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 类型：</span><span class="sxs-lookup"><span data-stu-id="f45db-211">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="f45db-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是 <xref:Microsoft.AspNetCore.Components.ComponentBase> 类型的抽象、可释放子级，其具有 <xref:System.IServiceProvider> 类型的受保护的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 属性。</span><span class="sxs-lookup"><span data-stu-id="f45db-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f45db-213">此提供程序可用于解析范围限定为组件生存期的服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-213">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="f45db-214">使用 [`@inject`](xref:mvc/views/razor#inject) 或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性注入到组件中的 DI 服务不在组件的范围内创建。</span><span class="sxs-lookup"><span data-stu-id="f45db-214">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="f45db-215">要使用组件的范围，必须使用 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 或 <xref:System.IServiceProvider.GetService%2A> 解析服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-215">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span>

<span data-ttu-id="f45db-216">任何使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 提供程序进行解析的服务都具有从同一范围提供的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="f45db-216">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span> <span data-ttu-id="f45db-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> 派生自 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>，并添加从范围内 DI 提供程序返回 `T` 实例的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 属性。</span><span class="sxs-lookup"><span data-stu-id="f45db-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="f45db-218">当存在一项应用需要从使用组件范围的 DI 容器中获取的主服务时，不必使用 <xref:System.IServiceProvider> 的实例即可通过此类型便捷地访问 Scoped 服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-218">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="f45db-219"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 属性可用，因此应用可获取其他类型的服务（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="f45db-219">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

<span data-ttu-id="f45db-220">使用来自 DI 的实体框架 DbContext</span><span class="sxs-lookup"><span data-stu-id="f45db-220">Use of Entity Framework DbContext from DI</span></span> <span data-ttu-id="f45db-221">从 Web 应用中的 DI 检索的一种常见服务类型是实体框架 (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> 对象。</span><span class="sxs-lookup"><span data-stu-id="f45db-221">One common service type to retrieve from DI in web apps is Entity Framework (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> objects.</span></span>

* <span data-ttu-id="f45db-222">默认情况下，使用 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 注册 EF 服务会将 <xref:Microsoft.EntityFrameworkCore.DbContext> 添加为一项 Scoped 服务。</span><span class="sxs-lookup"><span data-stu-id="f45db-222">Registering EF services using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> adds the <xref:Microsoft.EntityFrameworkCore.DbContext> as a scoped service by default.</span></span>
* <span data-ttu-id="f45db-223">注册为 Scoped 服务可能会导致 Blazor 应用中出现问题，因为这会导致 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例生存期较长且跨应用共享。</span><span class="sxs-lookup"><span data-stu-id="f45db-223">Registering as a scoped service can lead to problems in Blazor apps because it causes <xref:Microsoft.EntityFrameworkCore.DbContext> instances to be long-lived and shared across the app.</span></span>

<span data-ttu-id="f45db-224"><xref:Microsoft.EntityFrameworkCore.DbContext> 不是线程安全的且不得同时使用。</span><span class="sxs-lookup"><span data-stu-id="f45db-224"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread-safe and must not be used concurrently.</span></span> <span data-ttu-id="f45db-225">根据应用的不同，使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 将 <xref:Microsoft.EntityFrameworkCore.DbContext> 的范围限制为单个组件可能会解决此问题。</span><span class="sxs-lookup"><span data-stu-id="f45db-225">Depending on the app, using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> to limit the scope of a <xref:Microsoft.EntityFrameworkCore.DbContext> to a single component *may* solve the issue.</span></span> <span data-ttu-id="f45db-226">如果组件不并行使用 <xref:Microsoft.EntityFrameworkCore.DbContext>，则从 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 派生该组件并从 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 检索 <xref:Microsoft.EntityFrameworkCore.DbContext> 就已足够，因为它可确保：</span><span class="sxs-lookup"><span data-stu-id="f45db-226">If a component doesn't use a <xref:Microsoft.EntityFrameworkCore.DbContext> in parallel, deriving the component from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and retrieving the <xref:Microsoft.EntityFrameworkCore.DbContext> from <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> is sufficient because it ensures that:</span></span>

* <span data-ttu-id="f45db-227">单独的组件不共享 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="f45db-227">Separate components don't share a <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="f45db-228"><xref:Microsoft.EntityFrameworkCore.DbContext> 的生存期与依赖它的组件的生存期一样长。</span><span class="sxs-lookup"><span data-stu-id="f45db-228">The <xref:Microsoft.EntityFrameworkCore.DbContext> lives only as long as the component depending on it.</span></span>
  * <span data-ttu-id="f45db-229">如果单个组件可能同时使用 <xref:Microsoft.EntityFrameworkCore.DbContext>（例如用户每次选择一个按钮），则即使使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 也不能避免并发 EF 操作问题。</span><span class="sxs-lookup"><span data-stu-id="f45db-229">If a single component might use a <xref:Microsoft.EntityFrameworkCore.DbContext> concurrently (for example, every time a user selects a button), even using <xref:Microsoft.AspNetCore.Components.OwningComponentBase> doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="f45db-230">在这种情况下，请对每个逻辑 EF 操作使用不同的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="f45db-230">In that case, use a different <xref:Microsoft.EntityFrameworkCore.DbContext> for each logical EF operation.</span></span> <span data-ttu-id="f45db-231">请使用下述任一方法：</span><span class="sxs-lookup"><span data-stu-id="f45db-231">Use either of the following approaches:</span></span> <span data-ttu-id="f45db-232">使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> 作为参数直接创建 <xref:Microsoft.EntityFrameworkCore.DbContext>，这可从 DI 进行检索且是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="f45db-232">Create the <xref:Microsoft.EntityFrameworkCore.DbContext> directly using <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="f45db-233">在具有 Transient 生存期的服务容器中注册 <xref:Microsoft.EntityFrameworkCore.DbContext>：</span><span class="sxs-lookup"><span data-stu-id="f45db-233">Register the <xref:Microsoft.EntityFrameworkCore.DbContext> in the service container with a transient lifetime:</span></span> <span data-ttu-id="f45db-234">注册上下文时，请使用 <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="f45db-234">When registering the context, use <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="f45db-235"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展方法采用两个 <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> 类型的可选参数。</span><span class="sxs-lookup"><span data-stu-id="f45db-235">The <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method takes two optional parameters of type <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime>.</span></span>

* <xref:fundamentals/dependency-injection>
* <span data-ttu-id="f45db-236">若要使用此方法，则只有 `contextLifetime` 参数需要设为 <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="f45db-236">To use this approach, only the `contextLifetime` parameter needs to be <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.</span></span>
* <xref:mvc/views/dependency-injection>
