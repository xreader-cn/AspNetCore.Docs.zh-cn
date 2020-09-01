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
ms.openlocfilehash: b71b742c8a60b4b563649baa181b8c332ff02501
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865197"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="9b448-103">具有 Entity Framework Core (EFCore) 的 ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9b448-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="9b448-104">依据：[Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="9b448-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9b448-105">Blazor Server 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="9b448-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="9b448-106">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="9b448-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="9b448-107">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="9b448-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="9b448-108">Blazor Server 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="9b448-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="9b448-109">本文介绍 Blazor Server 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="9b448-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="9b448-110">Blazor WebAssembly 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="9b448-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="9b448-111">本文不讲解在 Blazor WebAssembly 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="9b448-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="9b448-112">示例应用</span><span class="sxs-lookup"><span data-stu-id="9b448-112">Sample app</span></span>

<span data-ttu-id="9b448-113">该示例应用构建作为使用 EF Core 的 Blazor Server 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="9b448-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="9b448-114">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="9b448-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="9b448-115">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="9b448-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="9b448-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9b448-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9b448-117">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="9b448-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="9b448-118">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="9b448-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="9b448-119">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="9b448-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="9b448-120">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="9b448-121">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="9b448-122">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="9b448-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="9b448-123">若要检查完全运行的代码（包括 Razor 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="9b448-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="9b448-124">数据库访问</span><span class="sxs-lookup"><span data-stu-id="9b448-124">Database access</span></span>

<span data-ttu-id="9b448-125">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="9b448-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="9b448-126">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="9b448-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="9b448-127">在 Blazor Server 应用中，范围服务注册可能会出现问题，因为该实例在用户线路中的各个组件之间共享。</span><span class="sxs-lookup"><span data-stu-id="9b448-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="9b448-128"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="9b448-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="9b448-129">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="9b448-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="9b448-130">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="9b448-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="9b448-131">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="9b448-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="9b448-132">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="9b448-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="9b448-133">以下建议旨在提供在 Blazor Server 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="9b448-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="9b448-134">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="9b448-135">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="9b448-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="9b448-136">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="9b448-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="9b448-137">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="9b448-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="9b448-138">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，请[将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime)。</span><span class="sxs-lookup"><span data-stu-id="9b448-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="9b448-139">新建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="9b448-139">New DbContext instances</span></span>

<span data-ttu-id="9b448-140">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="9b448-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="9b448-141">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="9b448-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="9b448-142">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="9b448-143">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="9b448-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="9b448-144">EF Core 5.0 或更高版本提供了一个内置工厂用于创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="9b448-145">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b448-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="9b448-146">该代码使用扩展方法为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="9b448-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="9b448-147">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="9b448-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="9b448-148">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="9b448-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="9b448-149">![NOTE] `Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="9b448-149">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="9b448-150">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="9b448-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="9b448-151">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="9b448-151">Scope to the component lifetime</span></span>

<span data-ttu-id="9b448-152">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="9b448-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="9b448-153">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="9b448-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="9b448-154">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="9b448-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="9b448-155">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="9b448-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="9b448-156">示例应用可确保在释放组件时释放联系人：</span><span class="sxs-lookup"><span data-stu-id="9b448-156">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="9b448-157">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="9b448-158">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="9b448-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="9b448-159">Blazor Server 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="9b448-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="9b448-160">应用保持与服务器的持续连接，且用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="9b448-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="9b448-161">用户状态的一个示例是在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="9b448-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="9b448-162">Blazor Server 提供的唯一应用程序模型需要使用特殊方法来使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="9b448-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="9b448-163">本文介绍 Blazor Server 应用中的 EF Core。</span><span class="sxs-lookup"><span data-stu-id="9b448-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="9b448-164">Blazor WebAssembly 应用在可阻止大多数直接数据库连接的 WebAssembly 沙盒中运行。</span><span class="sxs-lookup"><span data-stu-id="9b448-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="9b448-165">本文不讲解在 Blazor WebAssembly 中运行 EF Core。</span><span class="sxs-lookup"><span data-stu-id="9b448-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="9b448-166">示例应用</span><span class="sxs-lookup"><span data-stu-id="9b448-166">Sample app</span></span>

<span data-ttu-id="9b448-167">该示例应用构建作为使用 EF Core 的 Blazor Server 应用的参考。</span><span class="sxs-lookup"><span data-stu-id="9b448-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="9b448-168">示例应用中有一个网格，其中具有排序和筛选、删除、添加和更新操作。</span><span class="sxs-lookup"><span data-stu-id="9b448-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="9b448-169">示例演示了如何使用 EF Core 来处理开放式并发。</span><span class="sxs-lookup"><span data-stu-id="9b448-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="9b448-170">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9b448-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9b448-171">示例使用本地 [SQLite](https://www.sqlite.org/index.html) 数据库，以使其可在任意平台上使用。</span><span class="sxs-lookup"><span data-stu-id="9b448-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="9b448-172">该示例还配置了数据库日志记录来显示生成的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="9b448-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="9b448-173">这是在 `appsettings.Development.json` 中配置的：</span><span class="sxs-lookup"><span data-stu-id="9b448-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="9b448-174">网格、添加和视图组件使用“每操作上下文”模式；在此模式下，将为每个操作创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="9b448-175">编辑组件使用“每组件上下文”模式；在此模式下，将为每个组件创建一个上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="9b448-176">本主题中的一些代码示例需要未显示的命名空间和服务。</span><span class="sxs-lookup"><span data-stu-id="9b448-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="9b448-177">若要检查完全运行的代码（包括 Razor 示例所需的 [`@using`](xref:mvc/views/razor#using) 和 [`@inject`](xref:mvc/views/razor#inject) 指令），请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample)。</span><span class="sxs-lookup"><span data-stu-id="9b448-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="9b448-178">数据库访问</span><span class="sxs-lookup"><span data-stu-id="9b448-178">Database access</span></span>

<span data-ttu-id="9b448-179">EF Core 依赖于 <xref:Microsoft.EntityFrameworkCore.DbContext> 来[配置数据库访问](/ef/core/miscellaneous/configuring-dbcontext)和充当[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)。</span><span class="sxs-lookup"><span data-stu-id="9b448-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="9b448-180">EF Core 为 ASP.NET Core 应用提供 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 扩展，这些应用在默认情况下将上下文注册为范围服务。</span><span class="sxs-lookup"><span data-stu-id="9b448-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="9b448-181">在 Blazor Server 应用中，这可能会出现问题，因为该实例是在用户线路中的各个组件之间共享的。</span><span class="sxs-lookup"><span data-stu-id="9b448-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="9b448-182"><xref:Microsoft.EntityFrameworkCore.DbContext> 并非线程安全，且不是为并发使用而设计的。</span><span class="sxs-lookup"><span data-stu-id="9b448-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="9b448-183">由于以下原因，现有生存期不适当：</span><span class="sxs-lookup"><span data-stu-id="9b448-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="9b448-184">单一实例在应用的所有用户之间共享状态，并导致不适当的并发使用。</span><span class="sxs-lookup"><span data-stu-id="9b448-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="9b448-185">范围（默认）在同一用户的组件之间会造成类似的问题。</span><span class="sxs-lookup"><span data-stu-id="9b448-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="9b448-186">暂时性会导致每个请求均生成一个新实例；但由于组件的生存期很长，这会导致上下文生存期比预期更长。</span><span class="sxs-lookup"><span data-stu-id="9b448-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="9b448-187">数据库访问</span><span class="sxs-lookup"><span data-stu-id="9b448-187">Database access</span></span>

<span data-ttu-id="9b448-188">以下建议旨在提供在 Blazor Server 应用中使用 EF Core 的一致方法。</span><span class="sxs-lookup"><span data-stu-id="9b448-188">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="9b448-189">默认情况下，请考虑对每个操作使用一个上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-189">By default, consider using one context per operation.</span></span> <span data-ttu-id="9b448-190">上下文旨在实现快速、低开销的实例化：</span><span class="sxs-lookup"><span data-stu-id="9b448-190">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="9b448-191">使用标志来防止多个并发操作：</span><span class="sxs-lookup"><span data-stu-id="9b448-191">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="9b448-192">将操作放置在 `try` 块中 `Loading = true;` 行之后。</span><span class="sxs-lookup"><span data-stu-id="9b448-192">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="9b448-193">对于利用 EF Core 的[更改跟踪](/ef/core/querying/tracking)或[并发控制](/ef/core/saving/concurrency)的生存期较长的操作，[请将上下文范围限制为组件的生存期](#scope-to-the-component-lifetime)。</span><span class="sxs-lookup"><span data-stu-id="9b448-193">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="9b448-194">创建新的 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="9b448-194">Create new DbContext instances</span></span>

<span data-ttu-id="9b448-195">要新建 <xref:Microsoft.EntityFrameworkCore.DbContext> 实例，最快的方法是使用 `new` 创建新实例。</span><span class="sxs-lookup"><span data-stu-id="9b448-195">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="9b448-196">但是，存在几种可能需要解析其他依赖项的方案。</span><span class="sxs-lookup"><span data-stu-id="9b448-196">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="9b448-197">例如，你可能想要使用 [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) 来配置上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-197">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="9b448-198">要新建具有依赖项的 <xref:Microsoft.EntityFrameworkCore.DbContext>，推荐的解决方案是使用工厂。</span><span class="sxs-lookup"><span data-stu-id="9b448-198">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="9b448-199">示例应用在 `Data/DbContextFactory.cs` 中实现自己的工厂。</span><span class="sxs-lookup"><span data-stu-id="9b448-199">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="9b448-200">在前面的工厂中，<xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 通过服务提供程序满足任意依赖项。</span><span class="sxs-lookup"><span data-stu-id="9b448-200">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="9b448-201">以下示例会配置 [SQLite](https://www.sqlite.org/index.html) 并启用数据日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b448-201">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="9b448-202">该代码使用扩展方法为 DI 配置数据库工厂并提供默认选项：</span><span class="sxs-lookup"><span data-stu-id="9b448-202">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="9b448-203">工厂注入到组件中并用于创建新实例。</span><span class="sxs-lookup"><span data-stu-id="9b448-203">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="9b448-204">例如，在 `Pages/Index.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="9b448-204">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="9b448-205">![NOTE] `Wrapper` 是对 `GridWrapper` 组件的[组件引用](xref:blazor/components/index#capture-references-to-components)。</span><span class="sxs-lookup"><span data-stu-id="9b448-205">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="9b448-206">请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)中的 `Index` 组件 (`Pages/Index.razor`)。</span><span class="sxs-lookup"><span data-stu-id="9b448-206">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="9b448-207">组件生存期的范围</span><span class="sxs-lookup"><span data-stu-id="9b448-207">Scope to the component lifetime</span></span>

<span data-ttu-id="9b448-208">你可能想要创建一个在组件生存期内存在的 <xref:Microsoft.EntityFrameworkCore.DbContext>。</span><span class="sxs-lookup"><span data-stu-id="9b448-208">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="9b448-209">这样，你就可将它用作[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)，并利用更改跟踪和并发性解决方案等内置功能。</span><span class="sxs-lookup"><span data-stu-id="9b448-209">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="9b448-210">你可使用工厂来创建上下文，并在组件的生存期内对其进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="9b448-210">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="9b448-211">首先，实现 <xref:System.IDisposable> 并注入工厂，如 `Pages/EditContact.razor` 所示：</span><span class="sxs-lookup"><span data-stu-id="9b448-211">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="9b448-212">示例应用可确保在释放组件时释放联系人：</span><span class="sxs-lookup"><span data-stu-id="9b448-212">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="9b448-213">最后，将替代 [`OnInitializedAsync`](xref:blazor/components/lifecycle) 来创建新的上下文。</span><span class="sxs-lookup"><span data-stu-id="9b448-213">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="9b448-214">在示例应用中，[`OnInitializedAsync`](xref:blazor/components/lifecycle) 将联系人加载到相同的方法中：</span><span class="sxs-lookup"><span data-stu-id="9b448-214">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="9b448-215">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="9b448-215">In the preceding example:</span></span>

* <span data-ttu-id="9b448-216">`Busy` 设置为 `true` 时，可能会开始异步操作。</span><span class="sxs-lookup"><span data-stu-id="9b448-216">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="9b448-217">将 `Busy` 设置回 `false` 时，异步操作应已完成。</span><span class="sxs-lookup"><span data-stu-id="9b448-217">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="9b448-218">在 `catch` 块中放置其他错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="9b448-218">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9b448-219">其他资源</span><span class="sxs-lookup"><span data-stu-id="9b448-219">Additional resources</span></span>

> [<span data-ttu-id="9b448-220">EF Core 文档</span><span class="sxs-lookup"><span data-stu-id="9b448-220">EF Core documentation</span></span>](/ef/)
