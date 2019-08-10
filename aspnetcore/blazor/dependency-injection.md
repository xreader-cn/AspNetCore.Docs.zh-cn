---
title: ASP.NET Core Blazor 依赖关系注入
author: guardrex
description: 了解 Blazor 应用如何将服务注入组件。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/dependency-injection
ms.openlocfilehash: a9330caa81eec0910206312283b3424c70cd1289
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68948387"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor 依赖关系注入

作者: [Rainer Stropek](https://www.timecockpit.com)

Blazor 支持[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)。 应用可以通过将内置服务注入组件来使用这些服务。 应用还可以定义和注册自定义服务, 并通过 DI 使其在整个应用中可用。

DI 是一种用于访问在中心位置配置的服务的技术。 在 Blazor 应用中, 这种方法可用于:

* 跨多个组件 (称为*单独*服务) 共享服务类的单个实例。
* 使用引用抽象将组件与具体服务类分离。 例如, 请考虑一个用于`IDataAccess`访问应用程序中的数据的接口。 接口由具体`DataAccess`类实现并在应用服务容器中注册为服务。 当组件使用 DI 接收`IDataAccess`实现时, 组件不与具体类型耦合。 可以交换实现, 这可能是单元测试中的模拟实现。

## <a name="default-services"></a>默认服务

默认服务会自动添加到应用的服务集合。

| 服务 | 生存期 | 描述 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | 单例 | 提供用于发送 HTTP 请求以及从 URI 所标识资源接收 HTTP 响应的方法。 请注意, 此实例`HttpClient`使用浏览器在后台处理 HTTP 流量。 [HttpClient](xref:System.Net.Http.HttpClient.BaseAddress)会自动设置为应用的基本 URI 前缀。 有关详细信息，请参阅 <xref:blazor/call-web-api> 。 |
| `IJSRuntime` | 单例 | 表示在其中调度 JavaScript 调用的 JavaScript 运行时的实例。 有关详细信息，请参阅 <xref:blazor/javascript-interop> 。 |
| `IUriHelper` | 单例 | 包含用于处理 Uri 和导航状态的帮助器。 有关详细信息, 请参阅[URI 和导航状态帮助](xref:blazor/routing#uri-and-navigation-state-helpers)程序。 |

自定义服务提供程序不会自动提供表中列出的默认服务。 如果使用自定义服务提供程序并且需要表中所示的任何服务, 请将所需服务添加到新的服务提供程序中。

## <a name="add-services-to-an-app"></a>向应用程序添加服务

创建新应用后, 请检查`Startup.ConfigureServices`方法:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

向`ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>传递方法, 该方法是服务描述符对象 () 的列表。 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 通过向服务集合提供服务描述符来添加服务。 下面的示例演示了`IDataAccess`接口及其具体实现`DataAccess`的概念:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

可以将服务配置为下表中所示的生存期。

| 生存期 | 描述 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor 客户端当前没有 DI 作用域的概念。 `Scoped`注册的服务的行为`Singleton`类似于服务。 但是, 服务器端承载模型支持`Scoped`生存期。 在 Razor 组件中, 作用域内服务注册的范围为连接。 出于此原因, 使用作用域内服务的目的是应该作用于当前用户的服务, 即使当前目的是在浏览器中运行客户端。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI 创建服务的*单个实例*。 所有需要服务的`Singleton`组件都接收相同服务的实例。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | 每当组件从服务容器获取`Transient`服务的实例时, 它都会接收服务的*新实例*。 |

DI 系统基于 ASP.NET Core 中的 DI 系统。 有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。

## <a name="request-a-service-in-a-component"></a>在组件中请求服务

将服务添加到服务集合后, 使用[ \@注入](xref:mvc/views/razor#inject)Razor 指令将服务注入到组件。 `@inject`具有两个参数:

* 键入&ndash;要注入的服务的类型。
* 属性&ndash;接收注入的应用服务的属性的名称。 属性不需要手动创建。 编译器将创建属性。

有关详细信息，请参阅 <xref:mvc/views/dependency-injection> 。

使用多`@inject`个语句注入不同的服务。

下面的示例说明如何使用 `@inject`。 服务实现`Services.IDataAccess`被注入到组件的属性`DataRepository`中。 请注意代码如何只使用`IDataAccess`抽象:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

在内部, 生成的属性`DataRepository`() `InjectAttribute`用特性修饰。 通常不会直接使用此属性。 如果基类对于组件是必需的, 并且插入的属性也是基类所必需的, 请手动添加`InjectAttribute`:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

在从基类派生的组件中, 指令`@inject`不是必需的。 `InjectAttribute`基类的可满足以下要求:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>在服务中使用 DI

复杂服务可能需要其他服务。 在前面的示例中`DataAccess` , 可能`HttpClient`需要默认服务。 `@inject`(或`InjectAttribute`) 不可用于服务。 必须改为使用*构造函数注入*。 通过将参数添加到服务的构造函数中, 添加了所需的服务。 当 DI 创建服务时, 它将在构造函数中识别它所需要的服务, 并相应地提供这些服务。

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

构造函数注入的先决条件:

* 一个构造函数必须存在, 其参数可以全部通过 DI 完成。 如果指定默认值, 则不允许使用 DI 未涵盖的其他参数。
* 适用的构造函数必须是*公共*的。
* 必须存在一个适用的构造函数。 如果出现多义性, DI 会引发异常。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
