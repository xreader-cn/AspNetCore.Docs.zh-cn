---
title: 具有 Entity Framework Core (EFCore) 的 ASP.NET Core Blazor Server
author: JeremyLikness
description: 有关使用 Blazor Server 应用中的 EF Core 的指南。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: 6fc8913640a0a8d506e2c00002912897edbfd826
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280489"
---
# <a name="aspnet-core-blazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="b1417-103">具有 Entity Framework Core (EFCore) 的 ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b1417-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b1417-104">Blazor Server 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="b1417-104">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b1417-105">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="b1417-105">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b1417-106">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="b1417-106">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b1417-107">Blazor Server 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="b1417-107">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b1417-108">本文介绍 Blazor Server 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b1417-108">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b1417-109">Blazor WebAssembly 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="b1417-109">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b1417-110">本文不讲解在 Blazor WebAssembly 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b1417-110">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="b1417-111">示例应用</span><span class="sxs-lookup"><span data-stu-id="b1417-111">Sample app</span></span></h2>

<span data-ttu-id="b1417-112">该示例应用构建作为使用 EF Core 的 Blazor Server 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="b1417-112">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b1417-113">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="b1417-113">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b1417-114">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="b1417-114">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b1417-115">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b1417-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b1417-116">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="b1417-116">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b1417-117">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="b1417-117">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b1417-118">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="b1417-118">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b1417-119">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-119">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b1417-120">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-120">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b1417-121">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="b1417-121">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b1417-122">若要检查完全运行的代码（包括 Razor 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="b1417-122">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="b1417-123">数据库访问</span><span class="sxs-lookup"><span data-stu-id="b1417-123">Database access</span></span></h2>

<span data-ttu-id="b1417-124">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="b1417-124">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b1417-125">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="b1417-125">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b1417-126">在 Blazor Server 应用中，范围服务注册可能会出现问题，因为该实例在用户线路中的各个组件之间共享。</span><span class="sxs-lookup"><span data-stu-id="b1417-126">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b1417-127"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="b1417-127"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b1417-128">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="b1417-128">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b1417-129">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="b1417-129">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b1417-130">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="b1417-130">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b1417-131">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="b1417-131">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b1417-132">以下建议旨在提供在 Blazor Server 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="b1417-132">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b1417-133">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-133">By default, consider using one context per operation.</span></span> <span data-ttu-id="b1417-134">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="b1417-134">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b1417-135">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="b1417-135">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="b1417-136">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="b1417-136">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b1417-137">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，请[将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="b1417-137">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="b1417-138">新建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="b1417-138">New DbContext instances</span></span></h3>

<span data-ttu-id="b1417-139">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b1417-139">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b1417-140">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="b1417-140">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b1417-141">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-141">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b1417-142">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="b1417-142">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b1417-143">EF Core 5.0 或更高版本提供了一个内置工厂用于创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-143">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="b1417-144">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="b1417-144">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b1417-145">该代码使用[扩展方法 (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) 为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="b1417-145">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b1417-146">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b1417-146">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b1417-147">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="b1417-147">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b1417-148">`Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="b1417-148">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b1417-149">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="b1417-149">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="b1417-150">可使用工厂创建新的 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，该工厂允许你为每个 `DbContext` 配置连接字符串，例如在使用 [ASP.NET Core 的 Identity 模型](xref:security/authentication/customize_identity_model)时：</span><span class="sxs-lookup"><span data-stu-id="b1417-150">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model](xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="b1417-151">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="b1417-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b1417-152">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="b1417-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b1417-153">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="b1417-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b1417-154">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b1417-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b1417-155">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="b1417-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b1417-156">示例应用可确保在释放组件时释放上下文：</span><span class="sxs-lookup"><span data-stu-id="b1417-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b1417-157">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b1417-158">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="b1417-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="b1417-159">启用敏感数据日志记录</span><span class="sxs-lookup"><span data-stu-id="b1417-159">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="b1417-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包括异常消息和框架日志记录中的应用程序数据。</span><span class="sxs-lookup"><span data-stu-id="b1417-160"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="b1417-161">记录的数据可以包括分配给实体实例属性的值，以及发送到数据库的命令的参数值。</span><span class="sxs-lookup"><span data-stu-id="b1417-161">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="b1417-162">使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 记录数据存在安全风险，因为它可能在记录对数据库执行的 SQL 语句时公开密码和其他个人身份信息 (PII)。</span><span class="sxs-lookup"><span data-stu-id="b1417-162">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="b1417-163">建议只在开发和测试过程中启用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>：</span><span class="sxs-lookup"><span data-stu-id="b1417-163">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="b1417-164">Blazor Server 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="b1417-164">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b1417-165">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="b1417-165">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b1417-166">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="b1417-166">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b1417-167">Blazor Server 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="b1417-167">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b1417-168">本文介绍 Blazor Server 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b1417-168">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="b1417-169">Blazor WebAssembly 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="b1417-169">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b1417-170">本文不讲解在 Blazor WebAssembly 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b1417-170">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="b1417-171">示例应用</span><span class="sxs-lookup"><span data-stu-id="b1417-171">Sample app</span></span></h2>

<span data-ttu-id="b1417-172">该示例应用构建作为使用 EF Core 的 Blazor Server 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="b1417-172">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="b1417-173">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="b1417-173">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b1417-174">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="b1417-174">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b1417-175">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b1417-175">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b1417-176">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="b1417-176">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b1417-177">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="b1417-177">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b1417-178">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="b1417-178">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b1417-179">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-179">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b1417-180">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-180">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b1417-181">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="b1417-181">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b1417-182">若要检查完全运行的代码（包括 Razor 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="b1417-182">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="b1417-183">数据库访问</span><span class="sxs-lookup"><span data-stu-id="b1417-183">Database access</span></span></h2>

<span data-ttu-id="b1417-184">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="b1417-184">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b1417-185">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="b1417-185">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b1417-186">在 Blazor Server 应用中，这可能会出现问题，因为该实例是在用户线路中的各个组件之间共享的。</span><span class="sxs-lookup"><span data-stu-id="b1417-186">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b1417-187"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="b1417-187"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b1417-188">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="b1417-188">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b1417-189">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="b1417-189">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b1417-190">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="b1417-190">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b1417-191">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="b1417-191">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b1417-192">以下建议旨在提供在 Blazor Server 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="b1417-192">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="b1417-193">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-193">By default, consider using one context per operation.</span></span> <span data-ttu-id="b1417-194">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="b1417-194">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  using var context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b1417-195">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="b1417-195">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="b1417-196">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="b1417-196">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b1417-197">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，请[将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="b1417-197">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="b1417-198">新建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="b1417-198">New DbContext instances</span></span></h3>

<span data-ttu-id="b1417-199">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b1417-199">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b1417-200">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="b1417-200">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b1417-201">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-201">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b1417-202">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="b1417-202">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b1417-203">示例应用在 `Data/DbContextFactory.cs` 中实现自己的工厂。</span><span class="sxs-lookup"><span data-stu-id="b1417-203">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="b1417-204">在前面的工厂中：</span><span class="sxs-lookup"><span data-stu-id="b1417-204">In the preceding factory:</span></span>

* <span data-ttu-id="b1417-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 通过服务提供程序满足任意依赖项。</span><span class="sxs-lookup"><span data-stu-id="b1417-205"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="b1417-206">可从 Core ASP.NET Core 5.0 或更高版本中使用 `IDbContextFactory`，以便[在 ASP.NET Core 3.x 示例应用中实现](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)此接口。</span><span class="sxs-lookup"><span data-stu-id="b1417-206">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="b1417-207">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="b1417-207">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b1417-208">该代码使用扩展方法为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="b1417-208">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b1417-209">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b1417-209">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b1417-210">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="b1417-210">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b1417-211">`Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="b1417-211">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b1417-212">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="b1417-212">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="b1417-213">可以使用工厂创建新的 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，该工厂允许你为每个 `DbContext` 配置连接字符串，如使用 [ASP.NET Core 的 Identity 模型] (xref:security/authentication/customize_identity_model) 时：</span><span class="sxs-lookup"><span data-stu-id="b1417-213">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's Identity model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="b1417-214">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="b1417-214">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b1417-215">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="b1417-215">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b1417-216">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="b1417-216">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b1417-217">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b1417-217">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b1417-218">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="b1417-218">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b1417-219">示例应用可确保在释放组件时释放上下文：</span><span class="sxs-lookup"><span data-stu-id="b1417-219">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b1417-220">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="b1417-220">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b1417-221">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="b1417-221">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="b1417-222">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="b1417-222">In the preceding example:</span></span>

* <span data-ttu-id="b1417-223">`Busy` 设置为 `true` 时，可能会开始异步操作。</span><span class="sxs-lookup"><span data-stu-id="b1417-223">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="b1417-224">将 `Busy` 设置回 `false` 时，异步操作应已完成。</span><span class="sxs-lookup"><span data-stu-id="b1417-224">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="b1417-225">在 `catch` 块中放置其他错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="b1417-225">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="b1417-226">启用敏感数据日志记录</span><span class="sxs-lookup"><span data-stu-id="b1417-226">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="b1417-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包括异常消息和框架日志记录中的应用程序数据。</span><span class="sxs-lookup"><span data-stu-id="b1417-227"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="b1417-228">记录的数据可以包括分配给实体实例属性的值，以及发送到数据库的命令的参数值。</span><span class="sxs-lookup"><span data-stu-id="b1417-228">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="b1417-229">使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 记录数据存在安全风险，因为它可能在记录对数据库执行的 SQL 语句时公开密码和其他个人身份信息 (PII)。</span><span class="sxs-lookup"><span data-stu-id="b1417-229">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk**, as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="b1417-230">建议只在开发和测试过程中启用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>：</span><span class="sxs-lookup"><span data-stu-id="b1417-230">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b1417-231">其他资源</span><span class="sxs-lookup"><span data-stu-id="b1417-231">Additional resources</span></span>

* [<span data-ttu-id="b1417-232">EF Core 文档</span><span class="sxs-lookup"><span data-stu-id="b1417-232">EF Core documentation</span></span>](/ef/)
