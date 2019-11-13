---
title: ASP.NET Core Blazor 依赖关系注入
author: guardrex
description: 了解 Blazor 应用如何将服务注入组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/dependency-injection
ms.openlocfilehash: a39d913636afc55ac9d831de923ba7ae8db1216b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963081"
---
# <a name="aspnet-core-opno-locblazor-dependency-injection"></a><span data-ttu-id="e2847-103">ASP.NET Core Blazor 依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="e2847-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="e2847-104">作者： [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="e2847-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="e2847-105"> 支持[依赖关系注入（DI）](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="e2847-105"> supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2847-106">应用可以通过将内置服务注入组件来使用这些服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="e2847-107">应用还可以定义和注册自定义服务，并通过 DI 使其在整个应用中可用。</span><span class="sxs-lookup"><span data-stu-id="e2847-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="e2847-108">DI 是一种用于访问在中心位置配置的服务的技术。</span><span class="sxs-lookup"><span data-stu-id="e2847-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="e2847-109">在 Blazor 应用程序中，这可能很有用：</span><span class="sxs-lookup"><span data-stu-id="e2847-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="e2847-110">跨多个组件（称为*单独*服务）共享服务类的单个实例。</span><span class="sxs-lookup"><span data-stu-id="e2847-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="e2847-111">使用引用抽象将组件与具体服务类分离。</span><span class="sxs-lookup"><span data-stu-id="e2847-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="e2847-112">例如，请考虑一个 `IDataAccess` 的接口，用于访问应用中的数据。</span><span class="sxs-lookup"><span data-stu-id="e2847-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="e2847-113">接口由具体 `DataAccess` 类实现并在应用的服务容器中注册为服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="e2847-114">当组件使用 DI 接收 `IDataAccess` 实现时，组件不与具体类型耦合。</span><span class="sxs-lookup"><span data-stu-id="e2847-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="e2847-115">可以交换实现，这可能是单元测试中的模拟实现。</span><span class="sxs-lookup"><span data-stu-id="e2847-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="e2847-116">默认服务</span><span class="sxs-lookup"><span data-stu-id="e2847-116">Default services</span></span>

<span data-ttu-id="e2847-117">默认服务会自动添加到应用的服务集合。</span><span class="sxs-lookup"><span data-stu-id="e2847-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="e2847-118">服务</span><span class="sxs-lookup"><span data-stu-id="e2847-118">Service</span></span> | <span data-ttu-id="e2847-119">生存期</span><span class="sxs-lookup"><span data-stu-id="e2847-119">Lifetime</span></span> | <span data-ttu-id="e2847-120">描述</span><span class="sxs-lookup"><span data-stu-id="e2847-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="e2847-121">单一实例</span><span class="sxs-lookup"><span data-stu-id="e2847-121">Singleton</span></span> | <span data-ttu-id="e2847-122">提供用于发送 HTTP 请求以及从 URI 所标识资源接收 HTTP 响应的方法。</span><span class="sxs-lookup"><span data-stu-id="e2847-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="e2847-123">请注意，此 `HttpClient` 实例使用浏览器在后台处理 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="e2847-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="e2847-124">[HttpClient](xref:System.Net.Http.HttpClient.BaseAddress)会自动设置为应用的基本 URI 前缀。</span><span class="sxs-lookup"><span data-stu-id="e2847-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="e2847-125">有关更多信息，请参见<xref:blazor/call-web-api>。</span><span class="sxs-lookup"><span data-stu-id="e2847-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="e2847-126">单一实例</span><span class="sxs-lookup"><span data-stu-id="e2847-126">Singleton</span></span> | <span data-ttu-id="e2847-127">表示在其中调度 JavaScript 调用的 JavaScript 运行时的实例。</span><span class="sxs-lookup"><span data-stu-id="e2847-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="e2847-128">有关更多信息，请参见<xref:blazor/javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="e2847-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="e2847-129">单一实例</span><span class="sxs-lookup"><span data-stu-id="e2847-129">Singleton</span></span> | <span data-ttu-id="e2847-130">包含用于处理 Uri 和导航状态的帮助器。</span><span class="sxs-lookup"><span data-stu-id="e2847-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="e2847-131">有关详细信息，请参阅[URI 和导航状态帮助](xref:blazor/routing#uri-and-navigation-state-helpers)程序。</span><span class="sxs-lookup"><span data-stu-id="e2847-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="e2847-132">自定义服务提供程序不会自动提供表中列出的默认服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="e2847-133">如果使用自定义服务提供程序并且需要表中所示的任何服务，请将所需服务添加到新的服务提供程序中。</span><span class="sxs-lookup"><span data-stu-id="e2847-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="e2847-134">向应用程序添加服务</span><span class="sxs-lookup"><span data-stu-id="e2847-134">Add services to an app</span></span>

<span data-ttu-id="e2847-135">创建新应用后，检查 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="e2847-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="e2847-136">向 `ConfigureServices` 方法传递 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，它是服务描述符对象（<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>）的列表。</span><span class="sxs-lookup"><span data-stu-id="e2847-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="e2847-137">通过向服务集合提供服务描述符来添加服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="e2847-138">下面的示例演示了 `IDataAccess` 接口的概念及其具体实现 `DataAccess`：</span><span class="sxs-lookup"><span data-stu-id="e2847-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="e2847-139">可以将服务配置为下表中所示的生存期。</span><span class="sxs-lookup"><span data-stu-id="e2847-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="e2847-140">生存期</span><span class="sxs-lookup"><span data-stu-id="e2847-140">Lifetime</span></span> | <span data-ttu-id="e2847-141">描述</span><span class="sxs-lookup"><span data-stu-id="e2847-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="e2847-142"> WebAssembly 应用当前没有 DI 作用域的概念。</span><span class="sxs-lookup"><span data-stu-id="e2847-142"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="e2847-143">`Scoped`注册的服务的行为类似于 `Singleton` 服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="e2847-144">但是，Blazor Server 宿主模型支持 `Scoped` 生存期。</span><span class="sxs-lookup"><span data-stu-id="e2847-144">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="e2847-145">在 Blazor Server apps 中，作用域内服务注册的范围为*连接*。</span><span class="sxs-lookup"><span data-stu-id="e2847-145">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="e2847-146">出于此原因，使用作用域内服务的目的是应该作用于当前用户的服务，即使当前目的是在浏览器中运行客户端。</span><span class="sxs-lookup"><span data-stu-id="e2847-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="e2847-147">DI 创建服务的*单个实例*。</span><span class="sxs-lookup"><span data-stu-id="e2847-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="e2847-148">需要 `Singleton` 服务的所有组件均可接收同一服务的实例。</span><span class="sxs-lookup"><span data-stu-id="e2847-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="e2847-149">每当组件从服务容器获取 `Transient` 服务的实例时，它都会接收服务的*新实例*。</span><span class="sxs-lookup"><span data-stu-id="e2847-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="e2847-150">DI 系统基于 ASP.NET Core 中的 DI 系统。</span><span class="sxs-lookup"><span data-stu-id="e2847-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="e2847-151">有关更多信息，请参见<xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="e2847-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="e2847-152">在组件中请求服务</span><span class="sxs-lookup"><span data-stu-id="e2847-152">Request a service in a component</span></span>

<span data-ttu-id="e2847-153">将服务添加到服务集合后，使用[\@插入](xref:mvc/views/razor#inject)Razor 指令将服务注入到组件。</span><span class="sxs-lookup"><span data-stu-id="e2847-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="e2847-154">`@inject` 有两个参数：</span><span class="sxs-lookup"><span data-stu-id="e2847-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="e2847-155">键入 &ndash; 要注入的服务的类型。</span><span class="sxs-lookup"><span data-stu-id="e2847-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="e2847-156">属性 &ndash; 接收插入的应用服务的属性的名称。</span><span class="sxs-lookup"><span data-stu-id="e2847-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="e2847-157">属性不需要手动创建。</span><span class="sxs-lookup"><span data-stu-id="e2847-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="e2847-158">编译器将创建属性。</span><span class="sxs-lookup"><span data-stu-id="e2847-158">The compiler creates the property.</span></span>

<span data-ttu-id="e2847-159">有关更多信息，请参见<xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="e2847-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="e2847-160">使用多 `@inject` 语句注入不同的服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="e2847-161">下面的示例说明如何使用 `@inject`。</span><span class="sxs-lookup"><span data-stu-id="e2847-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="e2847-162">将实现 `Services.IDataAccess` 的服务注入组件的属性中 `DataRepository`。</span><span class="sxs-lookup"><span data-stu-id="e2847-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="e2847-163">请注意代码如何只使用 `IDataAccess` 抽象：</span><span class="sxs-lookup"><span data-stu-id="e2847-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="e2847-164">在内部，生成的属性（`DataRepository`）是通过 `InjectAttribute` 特性修饰的。</span><span class="sxs-lookup"><span data-stu-id="e2847-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="e2847-165">通常不会直接使用此属性。</span><span class="sxs-lookup"><span data-stu-id="e2847-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="e2847-166">如果基类对于组件是必需的，并且插入的属性也是基类所必需的，请手动添加 `InjectAttribute`：</span><span class="sxs-lookup"><span data-stu-id="e2847-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="e2847-167">在派生自基类的组件中，不需要 `@inject` 指令。</span><span class="sxs-lookup"><span data-stu-id="e2847-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="e2847-168">基类的 `InjectAttribute` 是足够的：</span><span class="sxs-lookup"><span data-stu-id="e2847-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="e2847-169">在服务中使用 DI</span><span class="sxs-lookup"><span data-stu-id="e2847-169">Use DI in services</span></span>

<span data-ttu-id="e2847-170">复杂服务可能需要其他服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-170">Complex services might require additional services.</span></span> <span data-ttu-id="e2847-171">在前面的示例中，`DataAccess` 可能需要 `HttpClient` 的默认服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="e2847-172">`@inject` （或 `InjectAttribute`）不能在服务中使用。</span><span class="sxs-lookup"><span data-stu-id="e2847-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="e2847-173">必须改为使用*构造函数注入*。</span><span class="sxs-lookup"><span data-stu-id="e2847-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="e2847-174">通过将参数添加到服务的构造函数中，添加了所需的服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="e2847-175">当 DI 创建服务时，它将在构造函数中识别它所需要的服务，并相应地提供这些服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="e2847-176">构造函数注入的先决条件：</span><span class="sxs-lookup"><span data-stu-id="e2847-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="e2847-177">一个构造函数必须存在，其参数可以全部通过 DI 完成。</span><span class="sxs-lookup"><span data-stu-id="e2847-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="e2847-178">如果指定默认值，则不允许使用 DI 未涵盖的其他参数。</span><span class="sxs-lookup"><span data-stu-id="e2847-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="e2847-179">适用的构造函数必须是*公共*的。</span><span class="sxs-lookup"><span data-stu-id="e2847-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="e2847-180">必须存在一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="e2847-180">One applicable constructor must exist.</span></span> <span data-ttu-id="e2847-181">如果出现多义性，DI 会引发异常。</span><span class="sxs-lookup"><span data-stu-id="e2847-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="e2847-182">用于管理 DI 作用域的实用工具基组件类</span><span class="sxs-lookup"><span data-stu-id="e2847-182">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="e2847-183">在 ASP.NET Core 应用中，作用域内服务通常作用于当前请求。</span><span class="sxs-lookup"><span data-stu-id="e2847-183">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="e2847-184">请求完成后，DI 系统将释放任何作用域内或暂时性的服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-184">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="e2847-185">在 Blazor Server apps 中，请求范围将在客户端连接期间持续，这可能会导致暂时性和作用域内服务的运行时间比预期要长得多。</span><span class="sxs-lookup"><span data-stu-id="e2847-185">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="e2847-186">若要将服务范围限定于组件的生存期，可以使用 `OwningComponentBase` 和 `OwningComponentBase<TService>` 基类。</span><span class="sxs-lookup"><span data-stu-id="e2847-186">To scope services to the lifetime of a component, can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="e2847-187">这些基类公开了 `IServiceProvider` 类型的 `ScopedServices` 属性，该属性解析范围限制在组件生存期内的服务。</span><span class="sxs-lookup"><span data-stu-id="e2847-187">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="e2847-188">若要创作从 Razor 中的基类继承的组件，请使用 `@inherits` 指令。</span><span class="sxs-lookup"><span data-stu-id="e2847-188">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```cshtml
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
> <span data-ttu-id="e2847-189">使用 `@inject` 或 `InjectAttribute` 注入到组件中的服务不会在组件的作用域中创建，并绑定到请求范围。</span><span class="sxs-lookup"><span data-stu-id="e2847-189">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2847-190">其他资源</span><span class="sxs-lookup"><span data-stu-id="e2847-190">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
