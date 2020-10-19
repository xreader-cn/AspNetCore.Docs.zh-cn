---
title: 具有 Entity Framework Core (EFCore) 的 ASP.NET Core Blazor Server
author: JeremyLikness
description: 有关使用 Blazor Server 应用中的 EF Core 的指南。
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
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
ms.openlocfilehash: fc902cb5a82fda9fdbed09c40d66a846d9360f6a
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900734"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="14025-103">具有 Entity Framework Core (EFCore) 的 ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="14025-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="14025-104">依据：[Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="14025-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="14025-105">Blazor Server 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="14025-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="14025-106">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="14025-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="14025-107">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="14025-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="14025-108">Blazor Server 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="14025-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="14025-109">本文介绍 Blazor Server 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="14025-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="14025-110">Blazor WebAssembly 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="14025-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="14025-111">本文不讲解在 Blazor WebAssembly 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="14025-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="14025-112">示例应用</span><span class="sxs-lookup"><span data-stu-id="14025-112">Sample app</span></span></h2>

<span data-ttu-id="14025-113">该示例应用构建作为使用 EF Core 的 Blazor Server 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="14025-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="14025-114">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="14025-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="14025-115">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="14025-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="14025-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="14025-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="14025-117">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="14025-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="14025-118">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="14025-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="14025-119">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="14025-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="14025-120">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="14025-121">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="14025-122">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="14025-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="14025-123">若要检查完全运行的代码（包括 Razor 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="14025-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="14025-124">数据库访问</span><span class="sxs-lookup"><span data-stu-id="14025-124">Database access</span></span></h2>

<span data-ttu-id="14025-125">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="14025-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="14025-126">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="14025-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="14025-127">在 Blazor Server 应用中，范围服务注册可能会出现问题，因为该实例在用户线路中的各个组件之间共享。</span><span class="sxs-lookup"><span data-stu-id="14025-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="14025-128"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="14025-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="14025-129">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="14025-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="14025-130">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="14025-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="14025-131">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="14025-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="14025-132">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="14025-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="14025-133">以下建议旨在提供在 Blazor Server 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="14025-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="14025-134">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="14025-135">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="14025-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="14025-136">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="14025-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="14025-137">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="14025-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="14025-138">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，请[将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime-5x)。</span><span class="sxs-lookup"><span data-stu-id="14025-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="14025-139">新建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="14025-139">New DbContext instances</span></span></h3>

<span data-ttu-id="14025-140">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="14025-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="14025-141">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="14025-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="14025-142">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="14025-143">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="14025-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="14025-144">EF Core 5.0 或更高版本提供了一个内置工厂用于创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="14025-145">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="14025-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="14025-146">该代码使用[扩展方法 (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) 为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="14025-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="14025-147">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="14025-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="14025-148">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="14025-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="14025-149">`Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="14025-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="14025-150">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="14025-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="14025-151">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="14025-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="14025-152">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="14025-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="14025-153">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="14025-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="14025-154">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="14025-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="14025-155">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="14025-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="14025-156">示例应用可确保在释放组件时释放上下文：</span><span class="sxs-lookup"><span data-stu-id="14025-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="14025-157">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="14025-158">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="14025-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="14025-159">Blazor Server 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="14025-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="14025-160">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="14025-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="14025-161">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="14025-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="14025-162">Blazor Server 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="14025-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="14025-163">本文介绍 Blazor Server 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="14025-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="14025-164">Blazor WebAssembly 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="14025-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="14025-165">本文不讲解在 Blazor WebAssembly 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="14025-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="14025-166">示例应用</span><span class="sxs-lookup"><span data-stu-id="14025-166">Sample app</span></span></h2>

<span data-ttu-id="14025-167">该示例应用构建作为使用 EF Core 的 Blazor Server 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="14025-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="14025-168">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="14025-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="14025-169">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="14025-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="14025-170">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="14025-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="14025-171">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="14025-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="14025-172">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="14025-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="14025-173">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="14025-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="14025-174">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="14025-175">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="14025-176">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="14025-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="14025-177">若要检查完全运行的代码（包括 Razor 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="14025-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="14025-178">数据库访问</span><span class="sxs-lookup"><span data-stu-id="14025-178">Database access</span></span></h2>

<span data-ttu-id="14025-179">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="14025-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="14025-180">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="14025-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="14025-181">在 Blazor Server 应用中，这可能会出现问题，因为该实例是在用户线路中的各个组件之间共享的。</span><span class="sxs-lookup"><span data-stu-id="14025-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="14025-182"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="14025-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="14025-183">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="14025-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="14025-184">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="14025-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="14025-185">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="14025-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="14025-186">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="14025-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="14025-187">以下建议旨在提供在 Blazor Server 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="14025-187">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="14025-188">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-188">By default, consider using one context per operation.</span></span> <span data-ttu-id="14025-189">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="14025-189">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="14025-190">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="14025-190">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="14025-191">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="14025-191">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="14025-192">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，请[将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime-3x)。</span><span class="sxs-lookup"><span data-stu-id="14025-192">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="14025-193">新建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="14025-193">New DbContext instances</span></span></h3>

<span data-ttu-id="14025-194">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="14025-194">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="14025-195">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="14025-195">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="14025-196">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-196">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="14025-197">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="14025-197">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="14025-198">示例应用在 `Data/DbContextFactory.cs` 中实现自己的工厂。</span><span class="sxs-lookup"><span data-stu-id="14025-198">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="14025-199">在前面的工厂中：</span><span class="sxs-lookup"><span data-stu-id="14025-199">In the preceding factory:</span></span>

* <span data-ttu-id="14025-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 通过服务提供程序满足任意依赖项。</span><span class="sxs-lookup"><span data-stu-id="14025-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="14025-201">可从 Core ASP.NET Core 5.0 或更高版本中使用 `IDbContextFactory`，以便[在 ASP.NET Core 3.x 示例应用中实现](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs)此接口。</span><span class="sxs-lookup"><span data-stu-id="14025-201">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="14025-202">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="14025-202">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="14025-203">该代码使用扩展方法为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="14025-203">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="14025-204">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="14025-204">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="14025-205">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="14025-205">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="14025-206">`Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="14025-206">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="14025-207">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="14025-207">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="14025-208">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="14025-208">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="14025-209">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="14025-209">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="14025-210">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="14025-210">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="14025-211">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="14025-211">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="14025-212">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="14025-212">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="14025-213">示例应用可确保在释放组件时释放上下文：</span><span class="sxs-lookup"><span data-stu-id="14025-213">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="14025-214">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="14025-214">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="14025-215">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="14025-215">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="14025-216">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="14025-216">In the preceding example:</span></span>

* <span data-ttu-id="14025-217">`Busy` 设置为 `true` 时，可能会开始异步操作。</span><span class="sxs-lookup"><span data-stu-id="14025-217">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="14025-218">将 `Busy` 设置回 `false` 时，异步操作应已完成。</span><span class="sxs-lookup"><span data-stu-id="14025-218">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="14025-219">在 `catch` 块中放置其他错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="14025-219">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="14025-220">其他资源</span><span class="sxs-lookup"><span data-stu-id="14025-220">Additional resources</span></span>

* [<span data-ttu-id="14025-221">EF Core 文档</span><span class="sxs-lookup"><span data-stu-id="14025-221">EF Core documentation</span></span>](/ef/)
