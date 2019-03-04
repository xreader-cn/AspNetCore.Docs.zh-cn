---
title: 在 ASP.NET Core 依赖注入
author: guardrex
description: 了解 ASP.NET Core 如何实现依赖注入和如何使用它。
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 5e1522e0819d989a7029c2928c1c33624c1774c7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899354"
---
# <a name="dependency-injection-in-aspnet-core"></a>在 ASP.NET Core 依赖注入

作者：[Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com) 和 [Luke Latham](https://github.com/guardrex)

ASP.NET Core 支持依赖关系注入 (DI) 软件设计模式，这是一种在类及其依赖关系之间实现[控制反转 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技术。

有关特定于 MVC 控制器中依赖关系注入的详细信息，请参阅 <xref:mvc/controllers/dependency-injection>。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="overview-of-dependency-injection"></a>依赖关系注入概述

依赖项是另一个对象所需的任何对象。 使用应用中其他类所依赖的 `WriteMessage` 方法检查以下 `MyDependency` 类：

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

::: moniker range=">= aspnetcore-2.1"

可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。 `MyDependency` 类是 `IndexModel` 类的依赖项：

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

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

可以创建 `MyDependency` 类的实例以使 `WriteMessage` 方法可用于类。 `MyDependency` 类是 `HomeController` 类的依赖项：

```csharp
public class HomeController : Controller
{
    MyDependency _dependency = new MyDependency();

    public async Task<IActionResult> Index()
    {
        await _dependency.WriteMessage(
            "HomeController.Index created this message.");

        return View();
    }
}
```

::: moniker-end

该类创建并直接依赖于 `MyDependency` 实例。 代码依赖关系（如前面的示例）存在问题，应该避免使用，原因如下：

* 要用不同的实现替换 `MyDependency`，必须修改类。
* 如果 `MyDependency` 具有依赖关系，则必须由类对其进行配置。 在具有多个依赖于 `MyDependency` 的类的大型项目中，配置代码在整个应用中会变得分散。
* 这种实现很难进行单元测试。 应用应使用模拟或存根 `MyDependency` 类，该类不能使用此方法。

依赖关系注入通过以下方式解决了这些问题：

* 使用接口抽象化依赖关系实现。
* 注册服务容器中的依赖关系。 ASP.NET Core 提供了一个内置的服务容器 [IServiceProvider](/dotnet/api/system.iserviceprovider)。 服务已在应用的 `Startup.ConfigureServices` 方法中注册。
* 将服务注入到使用它的类的构造函数中。 框架负责创建依赖关系的实例，并在不再需要时对其进行处理。

在[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 接口定义了服务为应用提供的方法：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

此接口由具体类型 `MyDependency` 实现：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

`MyDependency` 在其构造函数中请求 [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1)。 以链式方式使用依赖关系注入并不罕见。 每个请求的依赖关系相应地请求其自己的依赖关系。 容器解析图中的依赖关系并返回完全解析的服务。 必须被解析的依赖关系的集合通常被称为“依赖关系树”、“依赖关系图”或“对象图”。

必须在服务容器中注册 `IMyDependency` 和 `ILogger<TCategoryName>`。 `IMyDependency` 已在 `Startup.ConfigureServices` 中注册。 `ILogger<TCategoryName>` 由日志记录抽象基础结构注册，因此它是框架默认注册的[框架提供的服务](#framework-provided-services)。

在示例应用中，使用具体类型 `MyDependency` 注册 `IMyDependency` 服务。 注册将服务生存期的范围限定为单个请求的生存期。 本主题后面将介绍[服务生存期](#service-lifetimes)。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> 每个 `services.Add{SERVICE_NAME}` 扩展方法添加（并可能配置）服务。 例如，`services.AddMvc()` 添加 Razor Pages 和 MVC 需要的服务。 我们建议应用遵循此约定。 将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。

如果服务的构造函数需要基元（如 `string`），则可以使用[配置](xref:fundamentals/configuration/index)或[选项模式](xref:fundamentals/configuration/options)注入基元：

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

通过使用服务并分配给私有字段的类的构造函数请求服务的实例。 该字段用于在整个类中根据需要访问服务。

在示例应用中，请求 `IMyDependency` 实例并用于调用服务的 `WriteMessage` 方法：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/MyDependencyController.cs?name=snippet1&highlight=3,5-8,13-14)]

::: moniker-end

## <a name="framework-provided-services"></a>框架提供的服务

`Startup.ConfigureServices` 方法负责定义应用使用的服务，包括 Entity Framework Core 和 ASP.NET Core MVC 等平台功能。 最初，提供给 `ConfigureServices` 的 `IServiceCollection` 定义了以下服务（具体取决于[配置主机的方式](xref:fundamentals/index#host)）：

| 服务类型 | 生存期 |
| ------------ | -------- |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | 暂时 |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | 单例 |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | 单例 |
| [Microsoft.AspNetCore.Hosting.IStartup](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | 单例 |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | 暂时 |
| [Microsoft.AspNetCore.Hosting.Server.IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | 单例 |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | 暂时 |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](/dotnet/api/microsoft.extensions.logging.ilogger) | 单例 |
| [Microsoft.Extensions.Logging.ILoggerFactory](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | 单例 |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | 单一实例 |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | 暂时 |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.ioptions-1) | 单一实例 |
| [System.Diagnostics.DiagnosticSource](/dotnet/core/api/system.diagnostics.diagnosticsource) | 单一实例 |
| [System.Diagnostics.DiagnosticListener](/dotnet/core/api/system.diagnostics.diagnosticlistener) | 单例 |

当服务集合扩展方法可用于注册服务（及其依赖服务，如果需要）时，约定使用单个 `Add{SERVICE_NAME}` 扩展方法来注册该服务所需的所有服务。 以下代码是如何使用扩展方法 [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)、[AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity) 和 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc) 向容器添加其他服务的示例：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

有关详细信息，请参阅 API 文档中的 [ServiceCollection 类](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection)。

## <a name="service-lifetimes"></a>服务生存期

为每个注册的服务选择适当的生存期。 可以使用以下生存期配置 ASP.NET Core 服务：

**暂时**

暂时生存期服务是每次请求时创建的。 这种生存期适合轻量级、 无状态的服务。

**作用域（Scoped）**

作用域生存期服务以每个请求一次的方式创建。

> [!WARNING]
> 在中间件内使用有作用域的服务时，请将该服务注入至 `Invoke` 或 `InvokeAsync` 方法。 请不要通过构造函数注入进行注入，因为它会强制服务的行为与单一实例类似。 有关更多信息，请参见<xref:fundamentals/middleware/index>。

**单例**

单一实例生存期服务是在第一次请求时（或者在运行 `ConfigureServices` 并且使用服务注册指定实例时）创建的。 每个后续请求都使用相同的实例。 如果应用需要单一实例行为，建议允许服务容器管理服务的生存期。 不要实现单一实例设计模式并提供用户代码来管理对象在类中的生存期。

> [!WARNING]
> 从单一实例解析有作用域的服务很危险。 当处理后续请求时，它可能会导致服务处于不正确的状态。

### <a name="constructor-injection-behavior"></a>构造函数注入行为

服务可以通过两种机制来解析：

* `IServiceProvider`
* [ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; 允许在依赖关系注入容器中创建没有服务注册的对象。 `ActivatorUtilities` 用于面向用户的抽象，例如标记帮助器、MVC 控制器和模型绑定器。

构造函数可以接受依赖关系注入不提供的参数，但参数必须分配默认值。

当服务由 `IServiceProvider` 或 `ActivatorUtilities` 解析时，构造函数注入需要 *public* 构造函数。

当服务由 `ActivatorUtilities` 解析时，构造函数注入要求只存在一个适用的构造函数。 支持构造函数重载，但其参数可以全部通过依赖注入来实现的重载只能存在一个。

## <a name="entity-framework-contexts"></a>实体框架上下文

通常使用[设置了范围的生存期](#service-lifetimes)将实体框架上下文添加到服务容器中，因为 Web 应用数据库操作通常将范围设置为请求。 如果在注册数据库上下文时，<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> 重载未指定生存期，则设置默认生存期范围。 给定生存期的服务不应使用生存期比服务短的数据库上下文。

## <a name="lifetime-and-registration-options"></a>生存期和注册选项

为了演示生存期和注册选项之间的差异，请考虑以下接口，将任务表示为具有唯一标识符 `OperationId` 的操作。 根据为以下接口配置操作服务的生存期的方式，容器在类请求时提供相同或不同的服务实例：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

接口在 `Operation` 类中实现。 `Operation` 构造函数将生成一个 GUID（如果未提供）：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

注册 `OperationService` 取决于，每个其他 `Operation` 类型。 当通过依赖关系注入请求 `OperationService` 时，它将接收每个服务的新实例或基于从属服务的生存期的现有实例。

* 如果在请求时创建了临时服务，则 `IOperationTransient` 服务的 `OperationId` 与 `OperationService` 的 `OperationId` 不同。 `OperationService` 将接收 `IOperationTransient` 类的新实例。 新实例将生成一个不同的 `OperationId`。
* 如果按请求创建有作用域的服务，则 `IOperationScoped` 服务的 `OperationId` 与请求中 `OperationService` 的该 ID 相同。 在请求中，两个服务共享不同的 `OperationId` 值。
* 如果单一数据库和单一实例服务只创建一次并在所有请求和所有服务中使用，则 `OperationId` 在所有服务请求中保持不变。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

在 `Startup.ConfigureServices` 中，根据其指定的生存期，将每个类型添加到容器中：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=12-15,18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

`IOperationSingletonInstance` 服务正在使用已知 ID 为 `Guid.Empty` 的特定实例。 此类型在使用时很明显（其 GUID 全部为零）。

::: moniker range=">= aspnetcore-2.1"

示例应用演示了各个请求中和之间的对象生存期。 示例应用的 `IndexModel` 请求每种 `IOperation` 类型和 `OperationService`。 然后，页面通过属性分配显示所有页面模型类和服务的 `OperationId` 值：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

示例应用演示了各个请求中和之间的对象生存期。 示例应用包含一个 `OperationsController`，它会请求各种 `IOperation` 类型和 `OperationService`。 `Index` 操作将服务设置为 `ViewBag` 以显示服务的 `OperationId` 值：

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/OperationsController.cs?name=snippet1)]

::: moniker-end

以下两个输出显示了两个请求的结果：

**第一个请求：**

控制器操作：

暂时性：d233e165-f417-469b-a866-1cf1935d2518  
作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9  
实例：00000000-0000-0000-0000-000000000000

`OperationService` 操作：

暂时性：c6b049eb-1318-4e31-90f1-eb2dd849ff64  
作用域：5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9  
实例：00000000-0000-0000-0000-000000000000

**第二个请求：**

控制器操作：

暂时性：b63bd538-0a37-4ff1-90ba-081c5138dda0  
作用域：31e820c5-4834-4d22-83fc-a60118acb9f4  
单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9  
实例：00000000-0000-0000-0000-000000000000

`OperationService` 操作：

暂时性：c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
作用域：31e820c5-4834-4d22-83fc-a60118acb9f4  
单一实例：01271bc1-9e31-48e7-8f7c-7261b040ded9  
实例：00000000-0000-0000-0000-000000000000

观察哪个 `OperationId` 值会在一个请求之内和不同请求之间变化：

* 暂时性对象始终不同。 请注意，第一个和第二个请求的暂时性 `OperationId` 值对于 `OperationService` 操作和在请求内都是不同的。 为每个服务和请求提供了一个新实例。
* 有作用域的对象在一个请求内是相同的，但在请求之间是不同的。
* 单一实例对象对每个对象和每个请求都是相同的（不管 `ConfigureServices` 中是否提供 `Operation` 实例）。

## <a name="call-services-from-main"></a>从 main 调用服务

采用 [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) 创建 [IServiceScope ](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope)，以解析应用作用域中有作用域的服务。 此方法可以用于在启动时访问有作用域的服务以便运行初始化任务。 以下示例演示如何在 `Program.Main` 中获取 `MyScopedService` 的上下文：

```csharp
public static void Main(string[] args)
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

    host.Run();
}
```

## <a name="scope-validation"></a>作用域验证

::: moniker range=">= aspnetcore-2.0"

如果在开发环境中运行应用，默认的服务提供程序会执行检查，从而确认以下内容：

* 没有从根服务提供程序直接或间接解析到有作用域的服务。
* 未将有作用域的服务直接或间接注入到单一实例。

::: moniker-end

调用 [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) 时，会创建根服务提供程序。 在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。

有作用域的服务由创建它们的容器释放。 如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。 验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。

有关更多信息，请参见<xref:fundamentals/host/web-host#scope-validation>。

## <a name="request-services"></a>请求服务

来自 `HttpContext` 的 ASP.NET Core 请求中可用的服务通过 [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) 集合公开。

请求服务表示作为应用的一部分配置和请求的服务。 当对象指定依赖关系时，`RequestServices`（而不是 `ApplicationServices`）中的类型将满足这些要求。

通常，应用不应直接使用这些属性。 相反，通过类构造函数请求类所需的类型，并允许框架注入依赖关系。 这样生成的类更易于测试。

> [!NOTE]
> 与访问 `RequestServices` 集合相比，以构造函数参数的形式请求依赖项是更优先的选择。

## <a name="design-services-for-dependency-injection"></a>设计能够进行依赖关系注入的服务

最佳做法是：

* 设计服务以使用依赖关系注入来获取其依赖关系。
* 避免进行有状态的静态方法调用。
* 避免在服务中直接实例化依赖类。 直接实例化将代码耦合到特定实现。
* 不在应用类中包含过多内容，确保设计规范，并易于测试。

如果一个类似乎有过多的注入依赖关系，这通常表明该类拥有过多的责任并且违反了[单一责任原则 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。 尝试通过将某些职责移动到一个新类来重构类。 请记住，Razor Pages 页模型类和 MVC 控制器类应关注用户界面问题。 业务规则和数据访问实现细节应保留在适用于这些[分离的关注点](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)的类中。

### <a name="disposal-of-services"></a>服务处理

容器为其创建的 `IDisposable` 类型调用 `Dispose`。 如果通过用户代码将实例添加到容器中，则不会自动处理该实例。

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

::: moniker range="= aspnetcore-1.0"

> [!NOTE]
> 在 ASP.NET Core 1.0 中，容器将对所有 `IDisposable` 对象调用 dispose，包括那些并非由它创建的对象。

::: moniker-end

## <a name="default-service-container-replacement"></a>默认服务容器替换

内置的服务容器旨在满足框架和大多数消费者应用的需求。 我们建议使用内置容器，除非你需要的特定功能不受它支持。 内置容器中找不到第三方容器支持的某些功能：

* 属性注入
* 基于名称的注入
* 子容器
* 自定义生存期管理
* 对迟缓初始化的 `Func<T>` 支持

有关支持适配器的部分容器列表，请参阅[依赖关系注入 readme.md 文件](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection)。

以下示例将内置容器替换为 [Autofac](https://autofac.org/)：

* 安装适当的容器包：

  * [Autofac](https://www.nuget.org/packages/Autofac/)
  * [Autofac.Extensions.DependencyInjection](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* 在 `Startup.ConfigureServices` 中配置容器并返回 `IServiceProvider`：

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    要使用第三方容器，`Startup.ConfigureServices` 必须返回 `IServiceProvider`。

* 在 `DefaultModule` 中配置 Autofac：

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

在运行时，使用 Autofac 来解析类型，并注入依赖关系。 要了解有关结合使用 Autofac 和 ASP.NET Core 的详细信息，请参阅 [Autofac 文档](https://docs.autofac.org/en/latest/integration/aspnetcore.html)。

### <a name="thread-safety"></a>线程安全

单例服务需要是线程安全的。 如果单例服务依赖于一个瞬时服务，那么瞬时服务可能也需要是线程安全的，具体取决于单例使用它的方式。

单个服务的工厂方法，例如 [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__) 的第二个参数，不需要是线程安全的。 像类型 (`static`) 构造函数一样，它保证由单个线程调用一次。

## <a name="recommendations"></a>建议

* 不支持基于 `async/await` 和 `Task` 的服务解析。 C# 不支持异步构造函数，因此推荐的模式是在同步解析服务后使用异步方法。

* 避免在服务容器中直接存储数据和配置。 例如，用户的购物车通常不应添加到服务容器中。 配置应使用 [选项模型](xref:fundamentals/configuration/options)。 同样，避免"数据持有者"对象，也就是仅仅为实现对某些其他对象的访问而存在的对象。 最好通过 DI 请求实际项目。

* 避免静态访问服务（例如，静态键入 [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) 以便在其他地方使用）。

* 避免使用服务定位器模式。 例如，可以改为使用 DI 时，不要调用 <xref:System.IServiceProvider.GetService*> 来获取服务实例。 要避免的另一个服务定位器变体是注入可在运行时解析依赖项的工厂。 这两种做法混合了[控制反转](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。

* 避免静态访问 `HttpContext`（例如，[IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)）。

像任何一组建议一样，你可能会遇到需要忽略某建议的情况。 例外情况很少见 &mdash; 主要是框架本身内部的特殊情况。

DI 是静态/全局对象访问模式的替代方法。 如果将其与静态对象访问混合使用，则可能无法实现 DI 的优点。

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [在 ASP.NET Core 中使用依赖关系注入编写干净代码 (MSDN) ](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [容器托管的应用程序设计，序言：容器属于哪里？](https://blogs.msdn.microsoft.com/nblumhardt/2008/12/26/container-managed-application-design-prelude-where-does-the-container-belong/)
* [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）
* [控制反转容器和依赖关系注入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [如何在 ASP.NET Core DI 中注册具有多个接口的服务](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
