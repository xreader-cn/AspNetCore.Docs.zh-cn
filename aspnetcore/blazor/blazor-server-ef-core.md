---
title: '具有 Entity Framework Core (EFCore) 的 ASP.NET Core :::no-loc(Blazor Server):::'
author: JeremyLikness
description: '有关使用 :::no-loc(Blazor Server)::: 应用中的 EF Core 的指南。'
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: bfc8f334b9229fed54e6b9841e4fb255ed18249a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056616"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="b40f1-103">具有 Entity Framework Core (EFCore) 的 ASP.NET Core :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="b40f1-103">ASP.NET Core :::no-loc(Blazor Server)::: with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="b40f1-104">依据：[Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="b40f1-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b40f1-105">:::no-loc(Blazor Server)::: 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="b40f1-105">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="b40f1-106">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="b40f1-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b40f1-107">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="b40f1-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b40f1-108">:::no-loc(Blazor Server)::: 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="b40f1-108">The unique application model that :::no-loc(Blazor Server)::: provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b40f1-109">本文介绍 :::no-loc(Blazor Server)::: 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b40f1-109">This article addresses EF Core in :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="b40f1-110">:::no-loc(Blazor WebAssembly)::: 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="b40f1-110">:::no-loc(Blazor WebAssembly)::: apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b40f1-111">本文不讲解在 :::no-loc(Blazor WebAssembly)::: 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b40f1-111">Running EF Core in :::no-loc(Blazor WebAssembly)::: is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="b40f1-112">示例应用</span><span class="sxs-lookup"><span data-stu-id="b40f1-112">Sample app</span></span></h2>

<span data-ttu-id="b40f1-113">该示例应用构建作为使用 EF Core 的 :::no-loc(Blazor Server)::: 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="b40f1-113">The sample app was built as a reference for :::no-loc(Blazor Server)::: apps that use EF Core.</span></span> <span data-ttu-id="b40f1-114">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="b40f1-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b40f1-115">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="b40f1-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b40f1-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b40f1-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b40f1-117">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="b40f1-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b40f1-118">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="b40f1-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b40f1-119">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="b40f1-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b40f1-120">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b40f1-121">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b40f1-122">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="b40f1-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b40f1-123">若要检查完全运行的代码（包括 :::no-loc(Razor)::: 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for :::no-loc(Razor)::: examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="b40f1-124">数据库访问</span><span class="sxs-lookup"><span data-stu-id="b40f1-124">Database access</span></span></h2>

<span data-ttu-id="b40f1-125">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b40f1-126">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="b40f1-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b40f1-127">在 :::no-loc(Blazor Server)::: 应用中，范围服务注册可能会出现问题，因为该实例在用户线路中的各个组件之间共享。</span><span class="sxs-lookup"><span data-stu-id="b40f1-127">In :::no-loc(Blazor Server)::: apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b40f1-128"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="b40f1-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b40f1-129">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="b40f1-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b40f1-130">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="b40f1-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b40f1-131">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="b40f1-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b40f1-132">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="b40f1-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b40f1-133">以下建议旨在提供在 :::no-loc(Blazor Server)::: 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="b40f1-133">The following recommendations are designed to provide a consistent approach to using EF Core in :::no-loc(Blazor Server)::: apps.</span></span>

* <span data-ttu-id="b40f1-134">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="b40f1-135">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="b40f1-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b40f1-136">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="b40f1-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b40f1-137">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="b40f1-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b40f1-138">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，请[将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="b40f1-139">新建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="b40f1-139">New DbContext instances</span></span></h3>

<span data-ttu-id="b40f1-140">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b40f1-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b40f1-141">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="b40f1-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b40f1-142">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b40f1-143">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="b40f1-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b40f1-144">EF Core 5.0 或更高版本提供了一个内置工厂用于创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="b40f1-145">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="b40f1-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b40f1-146">该代码使用[扩展方法 (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/FactoryExtensions.cs) 为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="b40f1-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b40f1-147">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b40f1-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b40f1-148">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="b40f1-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b40f1-149">`Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b40f1-150">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="b40f1-151">可以使用工厂创建新的 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，该工厂允许你为每个 `DbContext` 配置连接字符串，如使用 [ASP.NET Core 的 :::no-loc(Identity)::: 模型] (xref:security/authentication/customize_identity_model) 时：</span><span class="sxs-lookup"><span data-stu-id="b40f1-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's :::no-loc(Identity)::: model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="b40f1-152">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="b40f1-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b40f1-153">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="b40f1-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b40f1-154">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="b40f1-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b40f1-155">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b40f1-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b40f1-156">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="b40f1-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b40f1-157">示例应用可确保在释放组件时释放上下文：</span><span class="sxs-lookup"><span data-stu-id="b40f1-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b40f1-158">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b40f1-159">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="b40f1-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="b40f1-160">启用敏感数据日志记录</span><span class="sxs-lookup"><span data-stu-id="b40f1-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="b40f1-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包括异常消息和框架日志记录中的应用程序数据。</span><span class="sxs-lookup"><span data-stu-id="b40f1-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="b40f1-162">记录的数据可以包括分配给实体实例属性的值，以及发送到数据库的命令的参数值。</span><span class="sxs-lookup"><span data-stu-id="b40f1-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="b40f1-163">使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 记录数据存在安全风险，因为它可能在记录对数据库执行的 SQL 语句时公开密码和其他个人身份信息 (PII)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk** , as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="b40f1-164">建议只在开发和测试过程中启用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>：</span><span class="sxs-lookup"><span data-stu-id="b40f1-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

<span data-ttu-id="b40f1-165">:::no-loc(Blazor Server)::: 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="b40f1-165">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="b40f1-166">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="b40f1-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="b40f1-167">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="b40f1-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="b40f1-168">:::no-loc(Blazor Server)::: 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="b40f1-168">The unique application model that :::no-loc(Blazor Server)::: provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b40f1-169">本文介绍 :::no-loc(Blazor Server)::: 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b40f1-169">This article addresses EF Core in :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="b40f1-170">:::no-loc(Blazor WebAssembly)::: 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="b40f1-170">:::no-loc(Blazor WebAssembly)::: apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="b40f1-171">本文不讲解在 :::no-loc(Blazor WebAssembly)::: 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="b40f1-171">Running EF Core in :::no-loc(Blazor WebAssembly)::: is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="b40f1-172">示例应用</span><span class="sxs-lookup"><span data-stu-id="b40f1-172">Sample app</span></span></h2>

<span data-ttu-id="b40f1-173">该示例应用构建作为使用 EF Core 的 :::no-loc(Blazor Server)::: 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="b40f1-173">The sample app was built as a reference for :::no-loc(Blazor Server)::: apps that use EF Core.</span></span> <span data-ttu-id="b40f1-174">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="b40f1-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="b40f1-175">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="b40f1-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="b40f1-176">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b40f1-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b40f1-177">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="b40f1-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="b40f1-178">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="b40f1-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="b40f1-179">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="b40f1-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="b40f1-180">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="b40f1-181">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="b40f1-182">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="b40f1-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="b40f1-183">若要检查完全运行的代码（包括 :::no-loc(Razor)::: 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for :::no-loc(Razor)::: examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="b40f1-184">数据库访问</span><span class="sxs-lookup"><span data-stu-id="b40f1-184">Database access</span></span></h2>

<span data-ttu-id="b40f1-185">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="b40f1-186">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="b40f1-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="b40f1-187">在 :::no-loc(Blazor Server)::: 应用中，这可能会出现问题，因为该实例是在用户线路中的各个组件之间共享的。</span><span class="sxs-lookup"><span data-stu-id="b40f1-187">In :::no-loc(Blazor Server)::: apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="b40f1-188"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="b40f1-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="b40f1-189">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="b40f1-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="b40f1-190">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="b40f1-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="b40f1-191">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="b40f1-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="b40f1-192">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="b40f1-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="b40f1-193">以下建议旨在提供在 :::no-loc(Blazor Server)::: 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="b40f1-193">The following recommendations are designed to provide a consistent approach to using EF Core in :::no-loc(Blazor Server)::: apps.</span></span>

* <span data-ttu-id="b40f1-194">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="b40f1-195">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="b40f1-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="b40f1-196">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="b40f1-196">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="b40f1-197">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="b40f1-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="b40f1-198">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，请[将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="b40f1-199">新建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="b40f1-199">New DbContext instances</span></span></h3>

<span data-ttu-id="b40f1-200">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b40f1-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="b40f1-201">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="b40f1-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="b40f1-202">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="b40f1-203">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="b40f1-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="b40f1-204">示例应用在 `Data/DbContextFactory.cs` 中实现自己的工厂。</span><span class="sxs-lookup"><span data-stu-id="b40f1-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="b40f1-205">在前面的工厂中：</span><span class="sxs-lookup"><span data-stu-id="b40f1-205">In the preceding factory:</span></span>

* <span data-ttu-id="b40f1-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 通过服务提供程序满足任意依赖项。</span><span class="sxs-lookup"><span data-stu-id="b40f1-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="b40f1-207">可从 Core ASP.NET Core 5.0 或更高版本中使用 `IDbContextFactory`，以便[在 ASP.NET Core 3.x 示例应用中实现](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/IDbContextFactory.cs)此接口。</span><span class="sxs-lookup"><span data-stu-id="b40f1-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="b40f1-208">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="b40f1-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="b40f1-209">该代码使用扩展方法为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="b40f1-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="b40f1-210">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="b40f1-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="b40f1-211">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="b40f1-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="b40f1-212">`Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="b40f1-213">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="b40f1-214">可以使用工厂创建新的 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，该工厂允许你为每个 `DbContext` 配置连接字符串，如使用 [ASP.NET Core 的 :::no-loc(Identity)::: 模型] (xref:security/authentication/customize_identity_model) 时：</span><span class="sxs-lookup"><span data-stu-id="b40f1-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's :::no-loc(Identity)::: model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="b40f1-215">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="b40f1-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="b40f1-216">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="b40f1-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="b40f1-217">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="b40f1-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="b40f1-218">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b40f1-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="b40f1-219">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="b40f1-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="b40f1-220">示例应用可确保在释放组件时释放上下文：</span><span class="sxs-lookup"><span data-stu-id="b40f1-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="b40f1-221">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="b40f1-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="b40f1-222">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="b40f1-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="b40f1-223">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="b40f1-223">In the preceding example:</span></span>

* <span data-ttu-id="b40f1-224">`Busy` 设置为 `true` 时，可能会开始异步操作。</span><span class="sxs-lookup"><span data-stu-id="b40f1-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="b40f1-225">将 `Busy` 设置回 `false` 时，异步操作应已完成。</span><span class="sxs-lookup"><span data-stu-id="b40f1-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="b40f1-226">在 `catch` 块中放置其他错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="b40f1-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="b40f1-227">启用敏感数据日志记录</span><span class="sxs-lookup"><span data-stu-id="b40f1-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="b40f1-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 包括异常消息和框架日志记录中的应用程序数据。</span><span class="sxs-lookup"><span data-stu-id="b40f1-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="b40f1-229">记录的数据可以包括分配给实体实例属性的值，以及发送到数据库的命令的参数值。</span><span class="sxs-lookup"><span data-stu-id="b40f1-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="b40f1-230">使用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> 记录数据存在安全风险，因为它可能在记录对数据库执行的 SQL 语句时公开密码和其他个人身份信息 (PII)。</span><span class="sxs-lookup"><span data-stu-id="b40f1-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk** , as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="b40f1-231">建议只在开发和测试过程中启用 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A>：</span><span class="sxs-lookup"><span data-stu-id="b40f1-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b40f1-232">其他资源</span><span class="sxs-lookup"><span data-stu-id="b40f1-232">Additional resources</span></span>

* [<span data-ttu-id="b40f1-233">EF Core 文档</span><span class="sxs-lookup"><span data-stu-id="b40f1-233">EF Core documentation</span></span>](/ef/)
