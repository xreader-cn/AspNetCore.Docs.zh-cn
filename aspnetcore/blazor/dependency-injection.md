---
title: ASP.NET Core Blazor 依赖关系注入
author: guardrex
description: 了解 Blazor 应用如何将服务注入组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/dependency-injection
ms.openlocfilehash: 17dd0f927064ae7c2b1e3e439fd93e2cb220a5a4
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879783"
---
# <a name="aspnet-core-opno-locblazor-dependency-injection"></a>ASP.NET Core Blazor 依赖关系注入

作者： [Rainer Stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 支持[依赖关系注入（DI）](xref:fundamentals/dependency-injection)。 应用可以通过将内置服务注入组件来使用这些服务。 应用还可以定义和注册自定义服务，并通过 DI 使其在整个应用中可用。

DI 是一种用于访问在中心位置配置的服务的技术。 在 Blazor 应用程序中，这可能很有用：

* 跨多个组件（称为*单独*服务）共享服务类的单个实例。
* 使用引用抽象将组件与具体服务类分离。 例如，请考虑用于访问应用中的数据的接口 `IDataAccess`。 接口由具体 `DataAccess` 类实现并在应用服务容器中注册为服务。 当组件使用 DI 接收 `IDataAccess` 实现时，组件不与具体类型耦合。 可以交换实现，这可能是单元测试中的模拟实现。

## <a name="default-services"></a>默认服务

默认服务会自动添加到应用的服务集合。

| 服务 | 生存期 | 描述 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | 单一实例 | 提供用于发送 HTTP 请求以及从 URI 所标识资源接收 HTTP 响应的方法。<br><br>Blazor WebAssembly 应用中 `HttpClient` 的实例使用浏览器在后台处理 HTTP 流量。<br><br>默认情况下，Blazor Server apps 不包括配置为服务的 `HttpClient`。 向 Blazor 服务器应用提供 `HttpClient`。<br><br>有关更多信息，请参见<xref:blazor/call-web-api>。 |
| `IJSRuntime` | 单一实例 | 表示在其中调度 JavaScript 调用的 JavaScript 运行时的实例。 有关更多信息，请参见<xref:blazor/javascript-interop>。 |
| `NavigationManager` | 单一实例 | 包含用于处理 Uri 和导航状态的帮助器。 有关详细信息，请参阅[URI 和导航状态帮助](xref:blazor/routing#uri-and-navigation-state-helpers)程序。 |

自定义服务提供程序不会自动提供表中列出的默认服务。 如果使用自定义服务提供程序并且需要表中所示的任何服务，请将所需服务添加到新的服务提供程序中。

## <a name="add-services-to-an-app"></a>向应用程序添加服务

创建新应用后，请检查 `Startup.ConfigureServices` 方法：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

向 `ConfigureServices` 方法传递 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，它是服务描述符对象（<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>）的列表。 通过向服务集合提供服务描述符来添加服务。 下面的示例演示了 `IDataAccess` 接口的概念及其具体实现 `DataAccess`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

可以将服务配置为下表中所示的生存期。

| 生存期 | 描述 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor WebAssembly 应用当前没有 DI 作用域的概念。 `Scoped`注册的服务的行为类似于 `Singleton` 服务。 但是，Blazor Server 宿主模型支持 `Scoped` 生存期。 在 Blazor Server apps 中，作用域内服务注册的范围为*连接*。 出于此原因，使用作用域内服务的目的是应该作用于当前用户的服务，即使当前目的是在浏览器中运行客户端。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI 创建服务的*单个实例*。 需要 `Singleton` 服务的所有组件均可接收同一服务的实例。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | 每当组件从服务容器获取 `Transient` 服务的实例时，它都会接收服务的*新实例*。 |

DI 系统基于 ASP.NET Core 中的 DI 系统。 有关更多信息，请参见<xref:fundamentals/dependency-injection>。

## <a name="request-a-service-in-a-component"></a>在组件中请求服务

将服务添加到服务集合后，使用[\@插入](xref:mvc/views/razor#inject)Razor 指令将服务注入到组件。 `@inject` 有两个参数：

* 键入要注入的服务的类型 &ndash;。
* 属性 &ndash; 接收插入的应用服务的属性的名称。 属性不需要手动创建。 编译器将创建属性。

有关更多信息，请参见<xref:mvc/views/dependency-injection>。

使用多个 `@inject` 语句注入不同的服务。

下面的示例说明如何使用 `@inject`。 将实现 `Services.IDataAccess` 的服务注入组件的属性 `DataRepository`。 请注意代码如何只使用 `IDataAccess` 抽象：

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

在内部，生成的属性（`DataRepository`）使用 `InjectAttribute` 属性。 通常不会直接使用此属性。 如果基类对于组件是必需的，并且插入的属性也是基类所必需的，请手动添加 `InjectAttribute`：

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

在派生自基类的组件中，不需要 `@inject` 指令。 基类的 `InjectAttribute` 是足够的：

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>在服务中使用 DI

复杂服务可能需要其他服务。 在前面的示例中，`DataAccess` 可能需要 `HttpClient` 的默认服务。 `@inject` （或 `InjectAttribute`）不能在服务中使用。 必须改为使用*构造函数注入*。 通过将参数添加到服务的构造函数中，添加了所需的服务。 当 DI 创建服务时，它将在构造函数中识别它所需要的服务，并相应地提供这些服务。

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

构造函数注入的先决条件：

* 一个构造函数必须存在，其参数可以全部通过 DI 完成。 如果指定默认值，则不允许使用 DI 未涵盖的其他参数。
* 适用的构造函数必须是*公共*的。
* 必须存在一个适用的构造函数。 如果出现多义性，DI 会引发异常。

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>用于管理 DI 作用域的实用工具基组件类

在 ASP.NET Core 应用中，作用域内服务通常作用于当前请求。 请求完成后，DI 系统将释放任何作用域内或暂时性的服务。 在 Blazor Server apps 中，请求范围将在客户端连接期间持续，这可能会导致暂时性和作用域内服务的运行时间比预期要长得多。

若要将服务范围限定于组件的生存期，可以使用 `OwningComponentBase` 和 `OwningComponentBase<TService>` 基类。 这些基类公开 `IServiceProvider` 类型的 `ScopedServices` 属性，该属性可解析范围限制在组件生存期内的服务。 若要创作从 Razor 中的基类继承的组件，请使用 `@inherits` 指令。

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
> 使用 `@inject` 或 `InjectAttribute` 注入到组件中的服务不会在组件的作用域中创建，并绑定到请求范围。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
