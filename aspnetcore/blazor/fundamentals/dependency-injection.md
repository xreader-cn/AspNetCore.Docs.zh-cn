---
title: ASP.NET Core Blazor 依赖关系注入
author: guardrex
description: 了解 Blazor 应用如何将服务注入组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: af6b645fc3c398414c85c78e1cfeb213e538c2a6
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506794"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="55921-103">ASP.NET Core Blazor 依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="55921-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="55921-104">作者：[Rainer Stropek](https://www.timecockpit.com) 和 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="55921-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="55921-105">[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 是一种技术，它用于访问配置在中心位置的服务：</span><span class="sxs-lookup"><span data-stu-id="55921-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="55921-106">可将框架注册的服务直接注入到 Blazor 应用的组件中。</span><span class="sxs-lookup"><span data-stu-id="55921-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="55921-107">Blazor 应用还可定义和注册自定义服务，并通过 DI 使其在整个应用中可用。</span><span class="sxs-lookup"><span data-stu-id="55921-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="55921-108">默认服务</span><span class="sxs-lookup"><span data-stu-id="55921-108">Default services</span></span>

<span data-ttu-id="55921-109">下表中所示的服务通常在 Blazor 应用中使用。</span><span class="sxs-lookup"><span data-stu-id="55921-109">The services shown the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="55921-110">服务</span><span class="sxs-lookup"><span data-stu-id="55921-110">Service</span></span> | <span data-ttu-id="55921-111">生存期</span><span class="sxs-lookup"><span data-stu-id="55921-111">Lifetime</span></span> | <span data-ttu-id="55921-112">描述</span><span class="sxs-lookup"><span data-stu-id="55921-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="55921-113">范围内</span><span class="sxs-lookup"><span data-stu-id="55921-113">Scoped</span></span> | <p><span data-ttu-id="55921-114">提供用于发送 HTTP 请求以及从 URI 标识的资源接收 HTTP 响应的方法。</span><span class="sxs-lookup"><span data-stu-id="55921-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="55921-115">Blazor WebAssembly 应用中 <xref:System.Net.Http.HttpClient> 的实例使用浏览器在后台处理 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="55921-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="55921-116">默认情况下，Blazor Server 应用不包含配置为服务的 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="55921-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="55921-117">向 Blazor Server 应用提供 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="55921-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="55921-118">有关详细信息，请参阅 <xref:blazor/call-web-api>。</span><span class="sxs-lookup"><span data-stu-id="55921-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="55921-119"><xref:System.Net.Http.HttpClient> 注册为作用域服务，而不是单一实例。</span><span class="sxs-lookup"><span data-stu-id="55921-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="55921-120">有关详细信息，请参阅[服务生存期](#service-lifetime)部分。</span><span class="sxs-lookup"><span data-stu-id="55921-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="55921-121">**Blazor WebAssembly** ：单例</span><span class="sxs-lookup"><span data-stu-id="55921-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="55921-122">**Blazor Server** ：作用域</span><span class="sxs-lookup"><span data-stu-id="55921-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="55921-123">表示在其中调度 JavaScript 调用的 JavaScript 运行时实例。</span><span class="sxs-lookup"><span data-stu-id="55921-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="55921-124">有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="55921-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="55921-125">**Blazor WebAssembly** ：单例</span><span class="sxs-lookup"><span data-stu-id="55921-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="55921-126">**Blazor Server** ：作用域</span><span class="sxs-lookup"><span data-stu-id="55921-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="55921-127">包含用于处理 URI 和导航状态的帮助程序。</span><span class="sxs-lookup"><span data-stu-id="55921-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="55921-128">有关详细信息，请参阅 [URI 和导航状态帮助程序](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)。</span><span class="sxs-lookup"><span data-stu-id="55921-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="55921-129">自定义服务提供程序不会自动提供表中列出的默认服务。</span><span class="sxs-lookup"><span data-stu-id="55921-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="55921-130">如果你使用自定义服务提供程序且需要表中所示的任何服务，请将所需服务添加到新的服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="55921-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="55921-131">向应用添加服务</span><span class="sxs-lookup"><span data-stu-id="55921-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="55921-132">在 `Program.cs` 的 `Program.Main` 方法中配置应用服务集合的服务。</span><span class="sxs-lookup"><span data-stu-id="55921-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="55921-133">在下例中，为 `IMyDependency` 注册了 `MyDependency` 实现：</span><span class="sxs-lookup"><span data-stu-id="55921-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

<span data-ttu-id="55921-134">生成主机后，可在呈现任何组件之前从根 DI 范围使用服务。</span><span class="sxs-lookup"><span data-stu-id="55921-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="55921-135">这对于在呈现内容之前运行初始化逻辑而言很有用：</span><span class="sxs-lookup"><span data-stu-id="55921-135">This can be useful for running initialization logic before rendering content:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

<span data-ttu-id="55921-136">主机会为应用提供中心配置实例。</span><span class="sxs-lookup"><span data-stu-id="55921-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="55921-137">在上述示例的基础上，天气服务的 URL 将从默认配置源（例如 `appsettings.json`）传递到 `InitializeWeatherAsync`：</span><span class="sxs-lookup"><span data-stu-id="55921-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

<span data-ttu-id="55921-138">创建新应用后，请检查 `Startup.cs` 中的 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="55921-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="55921-139">向 <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> 方法传递 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，它是[服务描述符](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor)对象列表。</span><span class="sxs-lookup"><span data-stu-id="55921-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="55921-140">通过向服务集合提供服务描述符来在 `ConfigureServices` 方法中添加服务。</span><span class="sxs-lookup"><span data-stu-id="55921-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="55921-141">下面的示例演示了 `IDataAccess` 接口的概念及其具体实现 `DataAccess`：</span><span class="sxs-lookup"><span data-stu-id="55921-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="55921-142">服务生存期</span><span class="sxs-lookup"><span data-stu-id="55921-142">Service lifetime</span></span>

<span data-ttu-id="55921-143">可使用下表中所示的生存期配置服务。</span><span class="sxs-lookup"><span data-stu-id="55921-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="55921-144">生存期</span><span class="sxs-lookup"><span data-stu-id="55921-144">Lifetime</span></span> | <span data-ttu-id="55921-145">描述</span><span class="sxs-lookup"><span data-stu-id="55921-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="55921-146">Blazor WebAssembly 应用当前没有 DI 范围的概念。</span><span class="sxs-lookup"><span data-stu-id="55921-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="55921-147">已注册 `Scoped` 的服务的行为与 `Singleton` 服务类似。</span><span class="sxs-lookup"><span data-stu-id="55921-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="55921-148">Blazor Server 托管模型在 HTTP 请求中支持 `Scoped` 生存期，但在客户端上加载的组件中的 SingalR 连接/线路消息中则不支持。</span><span class="sxs-lookup"><span data-stu-id="55921-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="55921-149">在页面或视图之间或从页面或视图导航到组件时，应用的 Razor 页面或 MVC 部分会正常处理作用域服务并在每个 HTTP 请求上重新创建服务。</span><span class="sxs-lookup"><span data-stu-id="55921-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="55921-150">在客户端上的组件间导航时，作用域服务不会重建，其中与服务器之间的通信通过用户线路的 SignalR 连接进行，而不是通过 HTTP 请求进行。</span><span class="sxs-lookup"><span data-stu-id="55921-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="55921-151">在客户端上的以下组件方案中，将重建作用域服务，因为为用户创建了新线路：</span><span class="sxs-lookup"><span data-stu-id="55921-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="55921-152">用户关闭了浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="55921-152">The user closes the browser's window.</span></span> <span data-ttu-id="55921-153">用户打开了一个新窗口，并向后导航到该应用。</span><span class="sxs-lookup"><span data-stu-id="55921-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="55921-154">用户在浏览器窗口中关闭应用的最后一个选项卡。</span><span class="sxs-lookup"><span data-stu-id="55921-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="55921-155">用户打开了一个新的选项卡，并向后导航到该应用。</span><span class="sxs-lookup"><span data-stu-id="55921-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="55921-156">用户选择浏览器的重新加载/刷新按钮。</span><span class="sxs-lookup"><span data-stu-id="55921-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="55921-157">若要详细了解如何在 Blazor Server 应用中跨作用域服务保留用户状态，请参阅 <xref:blazor/hosting-models?pivots=server>。</span><span class="sxs-lookup"><span data-stu-id="55921-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="55921-158">DI 创建服务的单个实例。</span><span class="sxs-lookup"><span data-stu-id="55921-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="55921-159">需要 `Singleton` 服务的所有组件都会接收同一服务的实例。</span><span class="sxs-lookup"><span data-stu-id="55921-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="55921-160">每当组件从服务容器获取 `Transient` 服务的实例时，它都会接收该服务的新实例。</span><span class="sxs-lookup"><span data-stu-id="55921-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="55921-161">DI 系统基于 ASP.NET Core 中的 DI 系统。</span><span class="sxs-lookup"><span data-stu-id="55921-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="55921-162">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="55921-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="55921-163">在组件中请求服务</span><span class="sxs-lookup"><span data-stu-id="55921-163">Request a service in a component</span></span>

<span data-ttu-id="55921-164">将服务添加到服务集合后，使用 [\@inject](xref:mvc/views/razor#inject) Razor 指令将服务注入组件。</span><span class="sxs-lookup"><span data-stu-id="55921-164">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="55921-165">[`@inject`](xref:mvc/views/razor#inject) 有两个参数：</span><span class="sxs-lookup"><span data-stu-id="55921-165">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="55921-166">类型：要注入的服务的类型。</span><span class="sxs-lookup"><span data-stu-id="55921-166">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="55921-167">属性：接收注入的应用服务的属性的名称。</span><span class="sxs-lookup"><span data-stu-id="55921-167">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="55921-168">属性无需手动创建。</span><span class="sxs-lookup"><span data-stu-id="55921-168">The property doesn't require manual creation.</span></span> <span data-ttu-id="55921-169">编译器会创建属性。</span><span class="sxs-lookup"><span data-stu-id="55921-169">The compiler creates the property.</span></span>

<span data-ttu-id="55921-170">有关详细信息，请参阅 <xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="55921-170">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="55921-171">使用多个 [`@inject`](xref:mvc/views/razor#inject) 语句来注入不同的服务。</span><span class="sxs-lookup"><span data-stu-id="55921-171">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="55921-172">下面的示例展示了如何使用 [`@inject`](xref:mvc/views/razor#inject)。</span><span class="sxs-lookup"><span data-stu-id="55921-172">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="55921-173">将实现 `Services.IDataAccess` 的服务注入组件的 `DataRepository` 属性中。</span><span class="sxs-lookup"><span data-stu-id="55921-173">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="55921-174">请注意代码是如何仅使用 `IDataAccess` 抽象的：</span><span class="sxs-lookup"><span data-stu-id="55921-174">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="55921-175">在内部，生成的属性 (`DataRepository`) 使用 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="55921-175">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="55921-176">通常，不直接使用此特性。</span><span class="sxs-lookup"><span data-stu-id="55921-176">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="55921-177">如果组件需要基类，并且基类也需要注入的属性，请手动添加 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性：</span><span class="sxs-lookup"><span data-stu-id="55921-177">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="55921-178">在派生自基类的组件中，不需要 [`@inject`](xref:mvc/views/razor#inject) 指令。</span><span class="sxs-lookup"><span data-stu-id="55921-178">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="55921-179">基类的 <xref:Microsoft.AspNetCore.Components.InjectAttribute> 就已足够：</span><span class="sxs-lookup"><span data-stu-id="55921-179">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="55921-180">在服务中使用 DI</span><span class="sxs-lookup"><span data-stu-id="55921-180">Use DI in services</span></span>

<span data-ttu-id="55921-181">复杂的服务可能需要其他服务。</span><span class="sxs-lookup"><span data-stu-id="55921-181">Complex services might require additional services.</span></span> <span data-ttu-id="55921-182">在下述示例中，`DataAccess` 需要 <xref:System.Net.Http.HttpClient> 默认服务。</span><span class="sxs-lookup"><span data-stu-id="55921-182">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="55921-183">[`@inject`](xref:mvc/views/razor#inject)（或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性）在服务中不可用。</span><span class="sxs-lookup"><span data-stu-id="55921-183">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="55921-184">必须改用构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="55921-184">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="55921-185">通过向服务的构造函数添加参数来添加所需服务。</span><span class="sxs-lookup"><span data-stu-id="55921-185">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="55921-186">当 DI 创建服务时，它会在构造函数中识别其所需的服务，并相应地提供这些服务。</span><span class="sxs-lookup"><span data-stu-id="55921-186">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="55921-187">在下面的示例中，构造函数通过 DI 接收 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="55921-187">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="55921-188"><xref:System.Net.Http.HttpClient> 是默认服务。</span><span class="sxs-lookup"><span data-stu-id="55921-188"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="55921-189">构造函数注入的先决条件：</span><span class="sxs-lookup"><span data-stu-id="55921-189">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="55921-190">必须存在一个构造函数，其参数可完全通过 DI 实现。</span><span class="sxs-lookup"><span data-stu-id="55921-190">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="55921-191">如果指定默认值，则允许使用 DI 未涵盖的其他参数。</span><span class="sxs-lookup"><span data-stu-id="55921-191">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="55921-192">适用的构造函数必须是 `public`。</span><span class="sxs-lookup"><span data-stu-id="55921-192">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="55921-193">必须存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="55921-193">One applicable constructor must exist.</span></span> <span data-ttu-id="55921-194">如果出现歧义，DI 会引发异常。</span><span class="sxs-lookup"><span data-stu-id="55921-194">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="55921-195">用于管理 DI 范围的实用工具基组件类</span><span class="sxs-lookup"><span data-stu-id="55921-195">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="55921-196">在 ASP.NET Core 应用中，Scoped 服务的范围通常限定为当前请求。</span><span class="sxs-lookup"><span data-stu-id="55921-196">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="55921-197">请求完成后，DI 系统将处置所有 Scoped 或 Transient 服务。</span><span class="sxs-lookup"><span data-stu-id="55921-197">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="55921-198">在 Blazor Server 应用中，请求范围会在客户端连接期间一直持续存在，这可能导致暂时性和范围内服务的生存期比预期要长得多。</span><span class="sxs-lookup"><span data-stu-id="55921-198">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="55921-199">在 Blazor WebAssembly 应用中，已注册范围内生存期的服务被视为单一实例，因此它们的生存期比典型 ASP.NET Core 应用中的范围内服务要长。</span><span class="sxs-lookup"><span data-stu-id="55921-199">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="55921-200">若要在应用中检测可释放的暂时性服务，请参阅[检测暂时性可释放对象](#detect-transient-disposables)部分。</span><span class="sxs-lookup"><span data-stu-id="55921-200">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="55921-201">限制 Blazor 应用中服务生存期的一种方法是使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 类型。</span><span class="sxs-lookup"><span data-stu-id="55921-201">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="55921-202"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 的一种抽象类型，它会创建与组件生存期相对应的 DI 范围。</span><span class="sxs-lookup"><span data-stu-id="55921-202"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="55921-203">通过使用此范围，可使用具有 Scoped 生存期的 DI 服务，并使其生存期与组件的生存期一样长。</span><span class="sxs-lookup"><span data-stu-id="55921-203">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="55921-204">销毁组件时，也会处置组件的 Scoped 服务提供程序提供的服务。</span><span class="sxs-lookup"><span data-stu-id="55921-204">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="55921-205">这对以下服务很有用：</span><span class="sxs-lookup"><span data-stu-id="55921-205">This can be useful for services that:</span></span>

* <span data-ttu-id="55921-206">由于 Transient 生存期不适用而应在组件中重复使用的服务。</span><span class="sxs-lookup"><span data-stu-id="55921-206">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="55921-207">由于 Singleton 生存期不适用而不得跨组件共享的服务。</span><span class="sxs-lookup"><span data-stu-id="55921-207">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="55921-208">可使用下面两个版本的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 类型：</span><span class="sxs-lookup"><span data-stu-id="55921-208">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="55921-209"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是 <xref:Microsoft.AspNetCore.Components.ComponentBase> 类型的抽象、可释放子级，其具有 <xref:System.IServiceProvider> 类型的受保护的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 属性。</span><span class="sxs-lookup"><span data-stu-id="55921-209"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="55921-210">此提供程序可用于解析范围限定为组件生存期的服务。</span><span class="sxs-lookup"><span data-stu-id="55921-210">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="55921-211">使用 [`@inject`](xref:mvc/views/razor#inject) 或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 特性注入到组件中的 DI 服务不在组件的范围内创建。</span><span class="sxs-lookup"><span data-stu-id="55921-211">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="55921-212">要使用组件的范围，必须使用 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 或 <xref:System.IServiceProvider.GetService%2A> 解析服务。</span><span class="sxs-lookup"><span data-stu-id="55921-212">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="55921-213">任何使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 提供程序进行解析的服务都具有从同一范围提供的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="55921-213">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <span data-ttu-id="55921-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> 派生自 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>，并添加从范围内 DI 提供程序返回 `T` 实例的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 属性。</span><span class="sxs-lookup"><span data-stu-id="55921-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="55921-215">当存在一项应用需要从使用组件范围的 DI 容器中获取的主服务时，不必使用 <xref:System.IServiceProvider> 的实例即可通过此类型便捷地访问 Scoped 服务。</span><span class="sxs-lookup"><span data-stu-id="55921-215">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="55921-216"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 属性可用，因此应用可获取其他类型的服务（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="55921-216">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="55921-217">使用来自 DI 的 Entity Framework Core (EF Core) DbContext</span><span class="sxs-lookup"><span data-stu-id="55921-217">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="55921-218">有关详细信息，请参阅 <xref:blazor/blazor-server-ef-core>。</span><span class="sxs-lookup"><span data-stu-id="55921-218">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

::: moniker range="< aspnetcore-5.0"

## <a name="detect-transient-disposables"></a><span data-ttu-id="55921-219">检测暂时性可释放对象</span><span class="sxs-lookup"><span data-stu-id="55921-219">Detect transient disposables</span></span>

<span data-ttu-id="55921-220">下面的示例演示如何在应使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 的应用中检测可释放的暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="55921-220">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="55921-221">有关详细信息，请参阅[用于管理 DI 范围的实用工具基组件类](#utility-base-component-classes-to-manage-a-di-scope)部分。</span><span class="sxs-lookup"><span data-stu-id="55921-221">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="55921-222">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="55921-222">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="55921-223">在以下示例中检测到 `TransientDisposable` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="55921-223">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

<!-- moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

moniker-end 

moniker range="< aspnetcore-5.0" -->

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

<!-- moniker-end -->

::: zone-end

::: zone pivot="server"

<span data-ttu-id="55921-224">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="55921-224">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="55921-225">向 `Program.cs` 添加 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 的命名空间：</span><span class="sxs-lookup"><span data-stu-id="55921-225">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="55921-226">在 `Program.cs` 的 `Program.CreateHostBuilder` 中：</span><span class="sxs-lookup"><span data-stu-id="55921-226">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

<span data-ttu-id="55921-227">在以下示例中检测到 `TransientDependency` (`Startup.cs`)：</span><span class="sxs-lookup"><span data-stu-id="55921-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="55921-228">其他资源</span><span class="sxs-lookup"><span data-stu-id="55921-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="55921-229">暂时和共享实例的 `IDisposable` 指南</span><span class="sxs-lookup"><span data-stu-id="55921-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
