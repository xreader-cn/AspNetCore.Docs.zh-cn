---
title: ASP.NET Core 中的选项模式
author: rick-anderson
description: 了解如何使用选项模式来表示 ASP.NET Core 应用中的相关设置组。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061088"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="0693f-103">ASP.NET Core 中的选项模式</span><span class="sxs-lookup"><span data-stu-id="0693f-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0693f-104">作者：[Kirk Larkin](https://twitter.com/serpent5) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)。</span><span class="sxs-lookup"><span data-stu-id="0693f-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="0693f-105">选项模式使用类来提供对相关设置组的强类型访问。</span><span class="sxs-lookup"><span data-stu-id="0693f-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="0693f-106">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="0693f-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="0693f-107">[接口分隔原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="0693f-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="0693f-108">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)：应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="0693f-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="0693f-109">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="0693f-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="0693f-110">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="0693f-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="0693f-111">本主题介绍 ASP.NET Core 中的选项模式。</span><span class="sxs-lookup"><span data-stu-id="0693f-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="0693f-112">若要了解如何在控制台应用中使用选项模式，请参阅 [.NET 中的选项模式](/dotnet/core/extensions/options)。</span><span class="sxs-lookup"><span data-stu-id="0693f-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="0693f-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0693f-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="0693f-114">绑定分层配置</span><span class="sxs-lookup"><span data-stu-id="0693f-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="0693f-115">选项接口</span><span class="sxs-lookup"><span data-stu-id="0693f-115">Options interfaces</span></span>

<span data-ttu-id="0693f-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="0693f-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="0693f-117">不\*_支持：_ 在应用启动后读取配置数据。</span><span class="sxs-lookup"><span data-stu-id="0693f-117">Does \***not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="0693f-118">命名选项</span><span class="sxs-lookup"><span data-stu-id="0693f-118">Named options</span></span>](#named)
* <span data-ttu-id="0693f-119">注册为[单一实例](xref:fundamentals/dependency-injection#singleton)且可以注入到任何[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="0693f-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="0693f-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="0693f-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="0693f-121">在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="0693f-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="0693f-122">有关详细信息，请参阅[使用 IOptionsSnapshot 读取已更新的数据](#ios)。</span><span class="sxs-lookup"><span data-stu-id="0693f-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="0693f-123">注册为[范围内](xref:fundamentals/dependency-injection#scoped)，因此无法注入到单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="0693f-124">支持[命名选项](#named)</span><span class="sxs-lookup"><span data-stu-id="0693f-124">Supports [named options](#named)</span></span>

<span data-ttu-id="0693f-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="0693f-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="0693f-126">用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="0693f-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="0693f-127">注册为[单一实例](xref:fundamentals/dependency-injection#singleton)且可以注入到任何[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="0693f-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="0693f-128">支持：</span><span class="sxs-lookup"><span data-stu-id="0693f-128">Supports:</span></span>
  * <span data-ttu-id="0693f-129">更改通知</span><span class="sxs-lookup"><span data-stu-id="0693f-129">Change notifications</span></span>
  * [<span data-ttu-id="0693f-130">命名选项</span><span class="sxs-lookup"><span data-stu-id="0693f-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="0693f-131">可重载配置</span><span class="sxs-lookup"><span data-stu-id="0693f-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="0693f-132">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="0693f-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="0693f-133">[后期配置](#options-post-configuration)方案允许在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="0693f-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="0693f-135">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="0693f-136">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="0693f-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="0693f-137">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="0693f-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="0693f-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="0693f-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="0693f-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="0693f-140">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="0693f-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="0693f-141">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="0693f-142">使用 IOptionsSnapshot 读取已更新的数据</span><span class="sxs-lookup"><span data-stu-id="0693f-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="0693f-143">通过使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>，针对请求生存期访问和缓存选项时，每个请求都会计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="0693f-144">当使用支持读取已更新的配置值的配置提供程序时，将在应用启动后读取对配置所做的更改。</span><span class="sxs-lookup"><span data-stu-id="0693f-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="0693f-145">`IOptionsMonitor` 和 `IOptionsSnapshot` 之间的区别在于：</span><span class="sxs-lookup"><span data-stu-id="0693f-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="0693f-146">`IOptionsMonitor` 是一种[单一示例服务](xref:fundamentals/dependency-injection#singleton)，可随时检索当前选项值，这在单一实例依赖项中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="0693f-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="0693f-147">`IOptionsSnapshot` 是一种[作用域服务](xref:fundamentals/dependency-injection#scoped)，并在构造 `IOptionsSnapshot<T>` 对象时提供选项的快照。</span><span class="sxs-lookup"><span data-stu-id="0693f-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="0693f-148">选项快照旨在用于暂时性和有作用域的依赖项。</span><span class="sxs-lookup"><span data-stu-id="0693f-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="0693f-149">以下代码使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="0693f-150">以下代码注册 `MyOptions` 绑定的配置实例：</span><span class="sxs-lookup"><span data-stu-id="0693f-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="0693f-151">在上面的代码中，已读取在应用启动后对 JSON 配置文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="0693f-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="0693f-152">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="0693f-152">IOptionsMonitor</span></span>

<span data-ttu-id="0693f-153">以下代码注册 `MyOptions` 绑定的配置实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="0693f-154">下面的示例使用 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>：</span><span class="sxs-lookup"><span data-stu-id="0693f-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="0693f-155">在上面的代码中，默认读取在应用启动后对 JSON 配置文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="0693f-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="0693f-156">命名选项支持使用 IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="0693f-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="0693f-157">命名选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-157">Named options:</span></span>

* <span data-ttu-id="0693f-158">当多个配置节绑定到同一属性时有用。</span><span class="sxs-lookup"><span data-stu-id="0693f-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="0693f-159">区分大小写。</span><span class="sxs-lookup"><span data-stu-id="0693f-159">Are case sensitive.</span></span>

<span data-ttu-id="0693f-160">请考虑以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="0693f-160">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="0693f-161">下面的类用于每个节，而不是创建两个类来绑定 `TopItem:Month` 和 `TopItem:Year`：</span><span class="sxs-lookup"><span data-stu-id="0693f-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="0693f-162">下面的代码将配置命名选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="0693f-163">下面的代码将显示命名选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="0693f-164">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-164">All options are named instances.</span></span> <span data-ttu-id="0693f-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向 `Options.DefaultName` 实例，即 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="0693f-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="0693f-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="0693f-167"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="0693f-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="0693f-168">`null` 命名选项用于面向所有命名实例，而不是某一特定命名实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="0693f-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定。</span><span class="sxs-lookup"><span data-stu-id="0693f-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="0693f-170">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="0693f-170">OptionsBuilder API</span></span>

<span data-ttu-id="0693f-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="0693f-172">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="0693f-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="0693f-173">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="0693f-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="0693f-174">`OptionsBuilder` 在[选项验证](#val)部分中使用。</span><span class="sxs-lookup"><span data-stu-id="0693f-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="0693f-175">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="0693f-175">Use DI services to configure options</span></span>

<span data-ttu-id="0693f-176">在配置选项时，可以通过以下两种方式通过依赖关系注入访问服务：</span><span class="sxs-lookup"><span data-stu-id="0693f-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="0693f-177">将配置委托传递给 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="0693f-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="0693f-178">`OptionsBuilder<TOptions>` 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="0693f-179">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="0693f-180">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="0693f-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="0693f-181">在调用 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 时，创建类型等效于框架执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0693f-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="0693f-182">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0693f-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="0693f-183">选项验证</span><span class="sxs-lookup"><span data-stu-id="0693f-183">Options validation</span></span>

<span data-ttu-id="0693f-184">通过选项验证，可以验证选项值。</span><span class="sxs-lookup"><span data-stu-id="0693f-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="0693f-185">请考虑以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="0693f-185">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="0693f-186">下面的类绑定到 `"MyConfig"` 配置节，并应用若干 `DataAnnotations` 规则：</span><span class="sxs-lookup"><span data-stu-id="0693f-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="0693f-187">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="0693f-187">The following code:</span></span>

* <span data-ttu-id="0693f-188">调用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> 以获取绑定到 `MyConfigOptions` 类的 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)。</span><span class="sxs-lookup"><span data-stu-id="0693f-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="0693f-189">调用 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> 以使用 `DataAnnotations` 启用验证。</span><span class="sxs-lookup"><span data-stu-id="0693f-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="0693f-190">`ValidateDataAnnotations` 扩展方法在 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet 包中定义。</span><span class="sxs-lookup"><span data-stu-id="0693f-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="0693f-191">对于使用 `Microsoft.NET.Sdk.Web` SDK 的 Web 应用，通过共享框架隐式引用此包。</span><span class="sxs-lookup"><span data-stu-id="0693f-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="0693f-192">下面的代码显示配置值或验证错误：</span><span class="sxs-lookup"><span data-stu-id="0693f-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="0693f-193">下面的代码使用委托应用更复杂的验证规则：</span><span class="sxs-lookup"><span data-stu-id="0693f-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="0693f-194">用于复杂验证的 IValidateOptions</span><span class="sxs-lookup"><span data-stu-id="0693f-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="0693f-195">下面的类实现了 <xref:Microsoft.Extensions.Options.IValidateOptions`1>：</span><span class="sxs-lookup"><span data-stu-id="0693f-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="0693f-196">`IValidateOptions` 允许将验证代码移出 `StartUp` 并将其移入类中。</span><span class="sxs-lookup"><span data-stu-id="0693f-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="0693f-197">使用前面的代码，使用以下代码在 `Startup.ConfigureServices` 中启用验证：</span><span class="sxs-lookup"><span data-stu-id="0693f-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="0693f-198">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="0693f-198">Options post-configuration</span></span>

<span data-ttu-id="0693f-199">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="0693f-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="0693f-200">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="0693f-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="0693f-202">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="0693f-203">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="0693f-203">Accessing options during startup</span></span>

<span data-ttu-id="0693f-204"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="0693f-205">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0693f-206">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="0693f-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="0693f-207">ConfigurationExtensions NuGet 包</span><span class="sxs-lookup"><span data-stu-id="0693f-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="0693f-208">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包已在 ASP.NET Core 应用中隐式引用。</span><span class="sxs-lookup"><span data-stu-id="0693f-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0693f-209">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="0693f-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="0693f-210">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="0693f-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="0693f-211">[接口分隔原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="0693f-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="0693f-212">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)：应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="0693f-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="0693f-213">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="0693f-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="0693f-214">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="0693f-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="0693f-215">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0693f-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0693f-216">先决条件</span><span class="sxs-lookup"><span data-stu-id="0693f-216">Prerequisites</span></span>

<span data-ttu-id="0693f-217">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="0693f-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="0693f-218">选项接口</span><span class="sxs-lookup"><span data-stu-id="0693f-218">Options interfaces</span></span>

<span data-ttu-id="0693f-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="0693f-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="0693f-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="0693f-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="0693f-221">更改通知</span><span class="sxs-lookup"><span data-stu-id="0693f-221">Change notifications</span></span>
* [<span data-ttu-id="0693f-222">命名选项</span><span class="sxs-lookup"><span data-stu-id="0693f-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="0693f-223">可重载配置</span><span class="sxs-lookup"><span data-stu-id="0693f-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="0693f-224">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="0693f-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="0693f-225">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="0693f-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="0693f-227">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="0693f-228">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="0693f-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="0693f-229">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="0693f-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="0693f-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="0693f-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="0693f-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="0693f-232">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="0693f-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="0693f-233">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="0693f-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="0693f-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="0693f-235">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="0693f-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="0693f-236"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="0693f-237">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="0693f-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="0693f-238">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="0693f-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="0693f-239">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="0693f-239">General options configuration</span></span>

<span data-ttu-id="0693f-240">常规选项配置已作为示例 1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="0693f-241">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="0693f-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="0693f-242">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="0693f-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="0693f-243">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="0693f-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="0693f-244">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-244">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="0693f-245">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="0693f-246">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="0693f-247">示例的 appsettings.json 文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="0693f-247">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="0693f-248">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="0693f-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="0693f-249">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="0693f-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="0693f-250">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="0693f-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="0693f-251">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="0693f-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="0693f-252">通过委托配置简单选项已作为示例 2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="0693f-253">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="0693f-253">Use a delegate to set options values.</span></span> <span data-ttu-id="0693f-254">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-254">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="0693f-255">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="0693f-256">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="0693f-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="0693f-257">Index.cshtml.cs:</span><span class="sxs-lookup"><span data-stu-id="0693f-257">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="0693f-258">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="0693f-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="0693f-259">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="0693f-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="0693f-260">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="0693f-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="0693f-261">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="0693f-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="0693f-262">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json 中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="0693f-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="0693f-263">当启用多个配置服务时，指定的最后一个配置源优于其他源，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="0693f-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="0693f-264">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="0693f-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="0693f-265">子选项配置</span><span class="sxs-lookup"><span data-stu-id="0693f-265">Suboptions configuration</span></span>

<span data-ttu-id="0693f-266">子选项配置已作为示例 3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="0693f-267">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="0693f-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="0693f-268">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="0693f-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="0693f-269">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="0693f-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="0693f-270">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json 中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="0693f-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="0693f-271">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="0693f-272">它将 `MySubOptions` 绑定到 appsettings.json 文件的部分 `subsection`：</span><span class="sxs-lookup"><span data-stu-id="0693f-272">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="0693f-273">`GetSection` 方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="0693f-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="0693f-274">示例的 appsettings.json 文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="0693f-274">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="0693f-275">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="0693f-276">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-276">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="0693f-277">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="0693f-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="0693f-278">选项注入</span><span class="sxs-lookup"><span data-stu-id="0693f-278">Options injection</span></span>

<span data-ttu-id="0693f-279">选项注入已作为示例 4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="0693f-280">将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入：</span><span class="sxs-lookup"><span data-stu-id="0693f-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="0693f-281">使用 [`@inject`](xref:mvc/views/razor#inject) Razor 指令的 Razor 页面或 MVC 视图。</span><span class="sxs-lookup"><span data-stu-id="0693f-281">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="0693f-282">页面或视图模型。</span><span class="sxs-lookup"><span data-stu-id="0693f-282">A page or view model.</span></span>

<span data-ttu-id="0693f-283">示例应用中的以下示例将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入页面模型 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="0693f-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="0693f-284">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="0693f-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="0693f-285">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="0693f-285">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="0693f-287">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="0693f-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="0693f-288">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="0693f-289">通过使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>，针对请求生存期访问和缓存选项时，每个请求都会计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="0693f-290">`IOptionsMonitor` 和 `IOptionsSnapshot` 之间的区别在于：</span><span class="sxs-lookup"><span data-stu-id="0693f-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="0693f-291">`IOptionsMonitor` 是一种[单一示例服务](xref:fundamentals/dependency-injection#singleton)，可随时检索当前选项值，这在单一实例依赖项中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="0693f-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="0693f-292">`IOptionsSnapshot` 是一种[作用域服务](xref:fundamentals/dependency-injection#scoped)，并在构造 `IOptionsSnapshot<T>` 对象时提供选项的快照。</span><span class="sxs-lookup"><span data-stu-id="0693f-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="0693f-293">选项快照旨在用于暂时性和有作用域的依赖项。</span><span class="sxs-lookup"><span data-stu-id="0693f-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="0693f-294">以下示例演示如何在更改 appsettings.json (Pages/Index.cshtml.cs) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="0693f-295">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json 文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="0693f-295">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="0693f-296">下图显示从 appsettings.json 文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="0693f-296">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="0693f-297">将 appsettings.json 文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="0693f-297">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="0693f-298">保存 appsettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0693f-298">Save the *appsettings.json* file.</span></span> <span data-ttu-id="0693f-299">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="0693f-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="0693f-300">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="0693f-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="0693f-301">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="0693f-302">命名选项支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="0693f-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="0693f-303">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="0693f-304">命名选项区分大小写。</span><span class="sxs-lookup"><span data-stu-id="0693f-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="0693f-305">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="0693f-306">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="0693f-307">从配置中提供从 appsettings.json 文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="0693f-307">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="0693f-308">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="0693f-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="0693f-309">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="0693f-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="0693f-310">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="0693f-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="0693f-311">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="0693f-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="0693f-312">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="0693f-313">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="0693f-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="0693f-314">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="0693f-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="0693f-315">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="0693f-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="0693f-316">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-316">All options are named instances.</span></span> <span data-ttu-id="0693f-317">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向 `Options.DefaultName` 实例，即 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="0693f-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="0693f-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="0693f-319"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="0693f-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="0693f-320">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="0693f-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="0693f-321">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="0693f-321">OptionsBuilder API</span></span>

<span data-ttu-id="0693f-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="0693f-323">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="0693f-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="0693f-324">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="0693f-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="0693f-325">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="0693f-325">Use DI services to configure options</span></span>

<span data-ttu-id="0693f-326">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="0693f-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="0693f-327">将配置委托传递给 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="0693f-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="0693f-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="0693f-329">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="0693f-330">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="0693f-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="0693f-331">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0693f-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="0693f-332">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0693f-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="0693f-333">选项验证</span><span class="sxs-lookup"><span data-stu-id="0693f-333">Options validation</span></span>

<span data-ttu-id="0693f-334">借助选项验证，可以验证已配置的选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="0693f-335">使用验证方法调用 `Validate`。如果选项有效，方法返回 `true`；如果无效，方法返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="0693f-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="0693f-336">上面的示例将命名选项实例设置为 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="0693f-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="0693f-337">默认选项实例为 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="0693f-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="0693f-338">选项验证在选项实例创建后运行。</span><span class="sxs-lookup"><span data-stu-id="0693f-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="0693f-339">系统保证在选项实例首次获得访问时通过验证。</span><span class="sxs-lookup"><span data-stu-id="0693f-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0693f-340">选项验证无法防止在创建选项实例后发生选项修改。</span><span class="sxs-lookup"><span data-stu-id="0693f-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="0693f-341">例如，在首次访问选项时按请求创建并验证 `IOptionsSnapshot` 选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="0693f-342">对于同一个请求，在后续访问尝试时不会再次验证 `IOptionsSnapshot` 选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="0693f-343">`Validate` 方法接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="0693f-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="0693f-344">若要完全自定义验证，请实现 `IValidateOptions<TOptions>`，它支持：</span><span class="sxs-lookup"><span data-stu-id="0693f-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="0693f-345">验证多种类型的选项：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="0693f-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="0693f-346">验证取决于其他选项类型：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="0693f-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="0693f-347">`IValidateOptions` 验证：</span><span class="sxs-lookup"><span data-stu-id="0693f-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="0693f-348">特定的命名选项实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-348">A specific named options instance.</span></span>
* <span data-ttu-id="0693f-349">所有选项（如果 `name` 为 `null` 的话）。</span><span class="sxs-lookup"><span data-stu-id="0693f-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="0693f-350">通过接口实现返回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="0693f-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="0693f-351">通过调用 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，可以从 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 包中获得基于数据注释的验证。</span><span class="sxs-lookup"><span data-stu-id="0693f-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="0693f-352">`Microsoft.Extensions.Options.DataAnnotations` 包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="0693f-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="0693f-353">设计团队正在考虑为未来版本提供预先验证（在启动时快速失败）。</span><span class="sxs-lookup"><span data-stu-id="0693f-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="0693f-354">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="0693f-354">Options post-configuration</span></span>

<span data-ttu-id="0693f-355">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="0693f-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="0693f-356">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="0693f-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="0693f-358">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="0693f-359">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="0693f-359">Accessing options during startup</span></span>

<span data-ttu-id="0693f-360"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="0693f-361">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0693f-362">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="0693f-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="0693f-363">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="0693f-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="0693f-364">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="0693f-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="0693f-365">[接口分隔原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="0693f-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="0693f-366">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)：应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="0693f-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="0693f-367">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="0693f-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="0693f-368">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="0693f-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="0693f-369">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0693f-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0693f-370">先决条件</span><span class="sxs-lookup"><span data-stu-id="0693f-370">Prerequisites</span></span>

<span data-ttu-id="0693f-371">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="0693f-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="0693f-372">选项接口</span><span class="sxs-lookup"><span data-stu-id="0693f-372">Options interfaces</span></span>

<span data-ttu-id="0693f-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="0693f-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="0693f-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="0693f-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="0693f-375">更改通知</span><span class="sxs-lookup"><span data-stu-id="0693f-375">Change notifications</span></span>
* [<span data-ttu-id="0693f-376">命名选项</span><span class="sxs-lookup"><span data-stu-id="0693f-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="0693f-377">可重载配置</span><span class="sxs-lookup"><span data-stu-id="0693f-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="0693f-378">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="0693f-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="0693f-379">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="0693f-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="0693f-381">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="0693f-382">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="0693f-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="0693f-383">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="0693f-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="0693f-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="0693f-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="0693f-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="0693f-386">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="0693f-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="0693f-387">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="0693f-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="0693f-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="0693f-389">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="0693f-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="0693f-390"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="0693f-391">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="0693f-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="0693f-392">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="0693f-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="0693f-393">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="0693f-393">General options configuration</span></span>

<span data-ttu-id="0693f-394">常规选项配置已作为示例 1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="0693f-395">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="0693f-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="0693f-396">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="0693f-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="0693f-397">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="0693f-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="0693f-398">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-398">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="0693f-399">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="0693f-400">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="0693f-401">示例的 appsettings.json 文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="0693f-401">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="0693f-402">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="0693f-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="0693f-403">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="0693f-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="0693f-404">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="0693f-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="0693f-405">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="0693f-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="0693f-406">通过委托配置简单选项已作为示例 2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="0693f-407">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="0693f-407">Use a delegate to set options values.</span></span> <span data-ttu-id="0693f-408">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-408">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="0693f-409">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="0693f-410">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="0693f-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="0693f-411">Index.cshtml.cs:</span><span class="sxs-lookup"><span data-stu-id="0693f-411">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="0693f-412">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="0693f-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="0693f-413">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="0693f-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="0693f-414">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="0693f-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="0693f-415">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="0693f-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="0693f-416">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json 中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="0693f-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="0693f-417">当启用多个配置服务时，指定的最后一个配置源优于其他源，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="0693f-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="0693f-418">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="0693f-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="0693f-419">子选项配置</span><span class="sxs-lookup"><span data-stu-id="0693f-419">Suboptions configuration</span></span>

<span data-ttu-id="0693f-420">子选项配置已作为示例 3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="0693f-421">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="0693f-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="0693f-422">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="0693f-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="0693f-423">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="0693f-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="0693f-424">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json 中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="0693f-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="0693f-425">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="0693f-426">它将 `MySubOptions` 绑定到 appsettings.json 文件的部分 `subsection`：</span><span class="sxs-lookup"><span data-stu-id="0693f-426">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="0693f-427">`GetSection` 方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="0693f-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="0693f-428">示例的 appsettings.json 文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="0693f-428">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="0693f-429">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="0693f-430">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="0693f-430">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="0693f-431">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="0693f-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="0693f-432">视图模型或通过直接视图注入提供的选项</span><span class="sxs-lookup"><span data-stu-id="0693f-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="0693f-433">视图模型或通过直接视图注入提供的选项已作为示例 4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="0693f-434">可在视图模型中或通过将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接注入到视图 (Pages/Index.cshtml.cs) 来提供选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="0693f-435">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="0693f-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="0693f-436">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="0693f-436">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="0693f-438">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="0693f-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="0693f-439">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="0693f-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支持包含最小处理开销的重新加载选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="0693f-441">针对请求生存期访问和缓存选项时，每个请求只能计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="0693f-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="0693f-442">以下示例演示如何在更改 appsettings.json (Pages/Index.cshtml.cs) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="0693f-443">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json 文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="0693f-443">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="0693f-444">下图显示从 appsettings.json 文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="0693f-444">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="0693f-445">将 appsettings.json 文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="0693f-445">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="0693f-446">保存 appsettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0693f-446">Save the *appsettings.json* file.</span></span> <span data-ttu-id="0693f-447">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="0693f-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="0693f-448">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="0693f-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="0693f-449">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="0693f-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="0693f-450">命名选项支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="0693f-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="0693f-451">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="0693f-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="0693f-452">命名选项区分大小写。</span><span class="sxs-lookup"><span data-stu-id="0693f-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="0693f-453">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="0693f-454">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="0693f-455">从配置中提供从 appsettings.json 文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="0693f-455">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="0693f-456">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="0693f-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="0693f-457">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="0693f-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="0693f-458">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="0693f-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="0693f-459">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="0693f-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="0693f-460">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="0693f-461">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="0693f-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="0693f-462">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="0693f-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="0693f-463">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="0693f-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="0693f-464">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-464">All options are named instances.</span></span> <span data-ttu-id="0693f-465">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向 `Options.DefaultName` 实例，即 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="0693f-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="0693f-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="0693f-467"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="0693f-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="0693f-468">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="0693f-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="0693f-469">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="0693f-469">OptionsBuilder API</span></span>

<span data-ttu-id="0693f-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="0693f-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="0693f-471">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="0693f-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="0693f-472">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="0693f-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="0693f-473">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="0693f-473">Use DI services to configure options</span></span>

<span data-ttu-id="0693f-474">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="0693f-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="0693f-475">将配置委托传递给 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="0693f-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="0693f-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="0693f-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="0693f-477">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="0693f-478">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="0693f-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="0693f-479">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0693f-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="0693f-480">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0693f-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="0693f-481">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="0693f-481">Options post-configuration</span></span>

<span data-ttu-id="0693f-482">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="0693f-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="0693f-483">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="0693f-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="0693f-485">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="0693f-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="0693f-486">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="0693f-486">Accessing options during startup</span></span>

<span data-ttu-id="0693f-487"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="0693f-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="0693f-488">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="0693f-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0693f-489">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="0693f-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0693f-490">其他资源</span><span class="sxs-lookup"><span data-stu-id="0693f-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
