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
# <a name="razor-components-dependency-injection"></a>Razor 组件依赖关系注入

通过[Rainer Stropek](https://www.timecockpit.com)

Razor 组件支持[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)。 应用程序可以通过将其注入到组件中使用内置的服务。 应用还可以定义和注册自定义服务并使其可在整个应用通过 DI。

## <a name="dependency-injection"></a>依赖关系注入

DI 是一种技术用于访问在一个中心位置中配置的服务。 这可以是 Razor 组件的应用程序中很有用：

* 在许多组件，称为之间共享单个服务类的实例*单独*服务。
* 通过使用引用抽象概念中分离出来具体的服务类中的组件。 例如，考虑一个接口`IDataAccess`用于访问应用中的数据。 通过具体的实现该接口`DataAccess`类，并注册为服务应用程序的服务容器中。 当一个组件使用 DI 接收`IDataAccess`实现，该组件不耦合到具体类型。 可以交换实现，可能是到单元测试中的模拟实现。

有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。

## <a name="add-services-to-di"></a>将服务添加到 DI

创建新的应用程序之后, 检查`Startup.ConfigureServices`方法：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

`ConfigureServices`方法传递<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，这是一系列服务描述符对象 (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>)。 通过提供到服务集合的服务描述符添加服务。 下面的示例演示了使用概念`IDataAccess`接口和其具体实现`DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

可以将服务配置下表中所示的生存期。

| 生存期 | 描述 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | 创建 DI*单个实例*的服务。 需要的所有组件`Singleton`服务接收相同的服务的实例。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | 每当组件获取的实例`Transient`服务从服务容器，它接收*新实例*的服务。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | 客户端 Blazor 当前没有 DI 作用域的概念。 `Scoped` 行为类似于`Singleton`。 但是，ASP.NET Core Razor 组件支持`Scoped`生存期。 在 Razor 组件中，作用域内的服务注册的应用范围限定为该连接。 出于此原因，作用域的服务最好使用的服务，应将范围设置为当前用户，即使当前的目的是客户端运行在浏览器中。 |

DI 系统基于 ASP.NET Core 中的 DI 系统。 有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。

## <a name="default-services"></a>默认服务

默认服务会自动添加到应用程序的服务集合。

| 服务 | 描述 |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | 提供用于发送 HTTP 请求和从由 URI （单一实例） 所标识资源接收 HTTP 响应的方法。 请注意，此实例的`HttpClient`使用浏览器来处理在后台的 HTTP 流量。 [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)自动设置为应用程序的基本 URI 前缀。 `HttpClient` 仅提供给客户端 Blazor 应用。 |
| `IJSRuntime` | 表示调用可能会派遣到其中一个 JavaScript 运行时的一个实例。 有关详细信息，请参阅 <xref:razor-components/javascript-interop>。 |
| `IUriHelper` | 包含使用 Uri 和导航状态 （单一实例） 的帮助程序。 `IUriHelper` 提供到 Blazor 和 Razor 组件应用程序。 |

就可以使用自定义服务提供程序而不是默认服务提供程序添加的默认模板。 自定义服务提供程序不会自动提供表中列出的默认服务。 如果要使用自定义服务提供程序，并且需要的任何服务表中所示，添加到新的服务提供程序所需的服务。

## <a name="request-a-service-in-a-component"></a>请求中组件的服务

服务添加到服务集合后，将服务注入到组件的 Razor 模板中使用[ @inject ](xref:mvc/views/razor#section-4) Razor 指令。 `@inject` 有两个参数：

* 类型名称：要插入的服务的类型。
* 属性名称：接收注入的应用服务的属性的名称。 请注意，该属性不需要手动创建。 编译器会创建该属性。

有关详细信息，请参阅 <xref:mvc/views/dependency-injection>。

使用多个`@inject`语句注入不同的服务。

下面的示例说明如何使用 `@inject`。 服务实现`Services.IDataAccess`注入到组件的属性`DataRepository`。 请注意，代码仅使用`IDataAccess`抽象：

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

在内部，则生成的属性 (`DataRepository`) 用修饰`InjectAttribute`属性。 通常情况下，此属性不是直接使用。 如果基类是所必需的组件和注入的属性也是必需的基类，`InjectAttribute`可以手动添加：

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

在组件派生自的基类，`@inject`指令不是必需的。 `InjectAttribute`基类的足以：

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a>在服务中的依赖关系注入

复杂的服务可能需要其他服务。 在前面的示例中，`DataAccess`可能需要`HttpClient`默认服务。 `@inject` (或`InjectAttribute`) 不是可在服务中使用。 *构造函数注入*必须改为使用。 通过将参数添加到服务的构造函数会添加所需的服务。 当依赖关系注入创建服务时，它认识到它需要在构造函数中，并相应地提供它们的服务。

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

* 必须有该形参的实参可以所有满足通过依赖关系注入的一个构造函数。 请注意，如果它们指定默认值，则允许未涵盖的 DI 的其他参数。
* 适用的构造函数必须是*公共*。
* 必须仅有一个适用的构造函数。 对于产生歧义，DI 将引发异常。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
