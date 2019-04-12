---
title: Razor 组件依赖关系注入
author: guardrex
description: 请参阅如何 Blazor 和 Razor 组件应用程序可以使用服务，通过将其注入到组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: razor-components/dependency-injection
ms.openlocfilehash: 40aec2e3a5032039c7d921f67d7d333b03c07fb1
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "59515360"
---
# <a name="razor-components-dependency-injection"></a><span data-ttu-id="42909-103">Razor 组件依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="42909-103">Razor Components dependency injection</span></span>

<span data-ttu-id="42909-104">通过[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="42909-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="42909-105">Razor 组件支持[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="42909-105">Razor Components supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="42909-106">应用程序可以通过将其注入到组件中使用内置的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="42909-107">应用还可以定义和注册自定义服务并使其可在整个应用通过 DI。</span><span class="sxs-lookup"><span data-stu-id="42909-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="42909-108">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="42909-108">Dependency injection</span></span>

<span data-ttu-id="42909-109">DI 是一种技术用于访问在一个中心位置中配置的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-109">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="42909-110">这可以是 Razor 组件的应用程序中很有用：</span><span class="sxs-lookup"><span data-stu-id="42909-110">This can be useful in Razor Components apps to:</span></span>

* <span data-ttu-id="42909-111">在许多组件，称为之间共享单个服务类的实例*单独*服务。</span><span class="sxs-lookup"><span data-stu-id="42909-111">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="42909-112">通过使用引用抽象概念中分离出来具体的服务类中的组件。</span><span class="sxs-lookup"><span data-stu-id="42909-112">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="42909-113">例如，考虑一个接口`IDataAccess`用于访问应用中的数据。</span><span class="sxs-lookup"><span data-stu-id="42909-113">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="42909-114">通过具体的实现该接口`DataAccess`类，并注册为服务应用程序的服务容器中。</span><span class="sxs-lookup"><span data-stu-id="42909-114">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="42909-115">当一个组件使用 DI 接收`IDataAccess`实现，该组件不耦合到具体类型。</span><span class="sxs-lookup"><span data-stu-id="42909-115">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="42909-116">可以交换实现，可能是到单元测试中的模拟实现。</span><span class="sxs-lookup"><span data-stu-id="42909-116">The implementation can be swapped, perhaps to a mock implementation in unit tests.</span></span>

<span data-ttu-id="42909-117">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="42909-117">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="add-services-to-di"></a><span data-ttu-id="42909-118">将服务添加到 DI</span><span class="sxs-lookup"><span data-stu-id="42909-118">Add services to DI</span></span>

<span data-ttu-id="42909-119">创建新的应用程序之后, 检查`Startup.ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="42909-119">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="42909-120">`ConfigureServices`方法传递<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，这是一系列服务描述符对象 (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>)。</span><span class="sxs-lookup"><span data-stu-id="42909-120">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="42909-121">通过提供到服务集合的服务描述符添加服务。</span><span class="sxs-lookup"><span data-stu-id="42909-121">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="42909-122">下面的示例演示了使用概念`IDataAccess`接口和其具体实现`DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="42909-122">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="42909-123">可以将服务配置下表中所示的生存期。</span><span class="sxs-lookup"><span data-stu-id="42909-123">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="42909-124">生存期</span><span class="sxs-lookup"><span data-stu-id="42909-124">Lifetime</span></span> | <span data-ttu-id="42909-125">描述</span><span class="sxs-lookup"><span data-stu-id="42909-125">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="42909-126">创建 DI*单个实例*的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-126">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="42909-127">需要的所有组件`Singleton`服务接收相同的服务的实例。</span><span class="sxs-lookup"><span data-stu-id="42909-127">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="42909-128">每当组件获取的实例`Transient`服务从服务容器，它接收*新实例*的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-128">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="42909-129">客户端 Blazor 当前没有 DI 作用域的概念。</span><span class="sxs-lookup"><span data-stu-id="42909-129">Client-side Blazor doesn't currently have the concept of DI scopes.</span></span> <span data-ttu-id="42909-130">`Scoped` 行为类似于`Singleton`。</span><span class="sxs-lookup"><span data-stu-id="42909-130">`Scoped` behaves like `Singleton`.</span></span> <span data-ttu-id="42909-131">但是，ASP.NET Core Razor 组件支持`Scoped`生存期。</span><span class="sxs-lookup"><span data-stu-id="42909-131">However, ASP.NET Core Razor Components support the `Scoped` lifetime.</span></span> <span data-ttu-id="42909-132">在 Razor 组件中，作用域内的服务注册的应用范围限定为该连接。</span><span class="sxs-lookup"><span data-stu-id="42909-132">In a Razor Component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="42909-133">出于此原因，作用域的服务最好使用的服务，应将范围设置为当前用户，即使当前的目的是客户端运行在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="42909-133">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |

<span data-ttu-id="42909-134">DI 系统基于 ASP.NET Core 中的 DI 系统。</span><span class="sxs-lookup"><span data-stu-id="42909-134">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="42909-135">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="42909-135">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="default-services"></a><span data-ttu-id="42909-136">默认服务</span><span class="sxs-lookup"><span data-stu-id="42909-136">Default services</span></span>

<span data-ttu-id="42909-137">默认服务会自动添加到应用程序的服务集合。</span><span class="sxs-lookup"><span data-stu-id="42909-137">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="42909-138">服务</span><span class="sxs-lookup"><span data-stu-id="42909-138">Service</span></span> | <span data-ttu-id="42909-139">描述</span><span class="sxs-lookup"><span data-stu-id="42909-139">Description</span></span> |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="42909-140">提供用于发送 HTTP 请求和从由 URI （单一实例） 所标识资源接收 HTTP 响应的方法。</span><span class="sxs-lookup"><span data-stu-id="42909-140">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI (singleton).</span></span> <span data-ttu-id="42909-141">请注意，此实例的`HttpClient`使用浏览器来处理在后台的 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="42909-141">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="42909-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)自动设置为应用程序的基本 URI 前缀。</span><span class="sxs-lookup"><span data-stu-id="42909-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="42909-143">`HttpClient` 仅提供给客户端 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="42909-143">`HttpClient` is only provided to client-side Blazor apps.</span></span> |
| `IJSRuntime` | <span data-ttu-id="42909-144">表示调用可能会派遣到其中一个 JavaScript 运行时的一个实例。</span><span class="sxs-lookup"><span data-stu-id="42909-144">Represents an instance of a JavaScript runtime to which calls may be dispatched.</span></span> <span data-ttu-id="42909-145">有关详细信息，请参阅 <xref:razor-components/javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="42909-145">For more information, see <xref:razor-components/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="42909-146">包含使用 Uri 和导航状态 （单一实例） 的帮助程序。</span><span class="sxs-lookup"><span data-stu-id="42909-146">Contains helpers for working with URIs and navigation state (singleton).</span></span> <span data-ttu-id="42909-147">`IUriHelper` 提供到 Blazor 和 Razor 组件应用程序。</span><span class="sxs-lookup"><span data-stu-id="42909-147">`IUriHelper` is provided to both Blazor and Razor Components apps.</span></span> |

<span data-ttu-id="42909-148">就可以使用自定义服务提供程序而不是默认服务提供程序添加的默认模板。</span><span class="sxs-lookup"><span data-stu-id="42909-148">It's possible to use a custom service provider instead of the default service provider added by the default template.</span></span> <span data-ttu-id="42909-149">自定义服务提供程序不会自动提供表中列出的默认服务。</span><span class="sxs-lookup"><span data-stu-id="42909-149">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="42909-150">如果要使用自定义服务提供程序，并且需要的任何服务表中所示，添加到新的服务提供程序所需的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-150">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="42909-151">请求中组件的服务</span><span class="sxs-lookup"><span data-stu-id="42909-151">Request a service in a component</span></span>

<span data-ttu-id="42909-152">服务添加到服务集合后，将服务注入到组件的 Razor 模板中使用[ @inject ](xref:mvc/views/razor#section-4) Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="42909-152">After services are added to the service collection, inject the services into the components' Razor templates using the [@inject](xref:mvc/views/razor#section-4) Razor directive.</span></span> <span data-ttu-id="42909-153">`@inject` 有两个参数：</span><span class="sxs-lookup"><span data-stu-id="42909-153">`@inject` has two parameters:</span></span>

* <span data-ttu-id="42909-154">类型名称：要插入的服务的类型。</span><span class="sxs-lookup"><span data-stu-id="42909-154">Type name: The type of the service to inject.</span></span>
* <span data-ttu-id="42909-155">属性名称：接收注入的应用服务的属性的名称。</span><span class="sxs-lookup"><span data-stu-id="42909-155">Property name: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="42909-156">请注意，该属性不需要手动创建。</span><span class="sxs-lookup"><span data-stu-id="42909-156">Note that the property doesn't require manual creation.</span></span> <span data-ttu-id="42909-157">编译器会创建该属性。</span><span class="sxs-lookup"><span data-stu-id="42909-157">The compiler creates the property.</span></span>

<span data-ttu-id="42909-158">有关详细信息，请参阅 <xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="42909-158">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="42909-159">使用多个`@inject`语句注入不同的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-159">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="42909-160">下面的示例说明如何使用 `@inject`。</span><span class="sxs-lookup"><span data-stu-id="42909-160">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="42909-161">服务实现`Services.IDataAccess`注入到组件的属性`DataRepository`。</span><span class="sxs-lookup"><span data-stu-id="42909-161">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="42909-162">请注意，代码仅使用`IDataAccess`抽象：</span><span class="sxs-lookup"><span data-stu-id="42909-162">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

<span data-ttu-id="42909-163">在内部，则生成的属性 (`DataRepository`) 用修饰`InjectAttribute`属性。</span><span class="sxs-lookup"><span data-stu-id="42909-163">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="42909-164">通常情况下，此属性不是直接使用。</span><span class="sxs-lookup"><span data-stu-id="42909-164">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="42909-165">如果基类是所必需的组件和注入的属性也是必需的基类，`InjectAttribute`可以手动添加：</span><span class="sxs-lookup"><span data-stu-id="42909-165">If a base class is required for components and injected properties are also required for the base class, `InjectAttribute` can be manually added:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // Dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="42909-166">在组件派生自的基类，`@inject`指令不是必需的。</span><span class="sxs-lookup"><span data-stu-id="42909-166">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="42909-167">`InjectAttribute`基类的足以：</span><span class="sxs-lookup"><span data-stu-id="42909-167">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a><span data-ttu-id="42909-168">在服务中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="42909-168">Dependency injection in services</span></span>

<span data-ttu-id="42909-169">复杂的服务可能需要其他服务。</span><span class="sxs-lookup"><span data-stu-id="42909-169">Complex services might require additional services.</span></span> <span data-ttu-id="42909-170">在前面的示例中，`DataAccess`可能需要`HttpClient`默认服务。</span><span class="sxs-lookup"><span data-stu-id="42909-170">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="42909-171">`@inject` (或`InjectAttribute`) 不是可在服务中使用。</span><span class="sxs-lookup"><span data-stu-id="42909-171">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="42909-172">*构造函数注入*必须改为使用。</span><span class="sxs-lookup"><span data-stu-id="42909-172">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="42909-173">通过将参数添加到服务的构造函数会添加所需的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-173">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="42909-174">当依赖关系注入创建服务时，它认识到它需要在构造函数中，并相应地提供它们的服务。</span><span class="sxs-lookup"><span data-stu-id="42909-174">When dependency injection creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="42909-175">构造函数注入的先决条件：</span><span class="sxs-lookup"><span data-stu-id="42909-175">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="42909-176">必须有该形参的实参可以所有满足通过依赖关系注入的一个构造函数。</span><span class="sxs-lookup"><span data-stu-id="42909-176">There must be one constructor whose arguments can all be fulfilled by dependency injection.</span></span> <span data-ttu-id="42909-177">请注意，如果它们指定默认值，则允许未涵盖的 DI 的其他参数。</span><span class="sxs-lookup"><span data-stu-id="42909-177">Note that additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="42909-178">适用的构造函数必须是*公共*。</span><span class="sxs-lookup"><span data-stu-id="42909-178">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="42909-179">必须仅有一个适用的构造函数。</span><span class="sxs-lookup"><span data-stu-id="42909-179">There must only be one applicable constructor.</span></span> <span data-ttu-id="42909-180">对于产生歧义，DI 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="42909-180">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42909-181">其他资源</span><span class="sxs-lookup"><span data-stu-id="42909-181">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
