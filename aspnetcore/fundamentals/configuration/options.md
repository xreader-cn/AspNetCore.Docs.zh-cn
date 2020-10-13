---
title: ASP.NET Core 中的选项模式
author: rick-anderson
description: 了解如何使用选项模式来表示 ASP.NET Core 应用中的相关设置组。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: 6eafad63bd4c49cee6a85108b37a8b61e1214bce
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762329"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="a9ed4-103">ASP.NET Core 中的选项模式</span><span class="sxs-lookup"><span data-stu-id="a9ed4-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9ed4-104">作者：[Kirk Larkin](https://twitter.com/serpent5) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="a9ed4-105">选项模式使用类来提供对相关设置组的强类型访问。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="a9ed4-106">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="a9ed4-107">[接口分隔原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="a9ed4-108">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)：应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="a9ed4-109">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="a9ed4-110">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="a9ed4-111">本主题介绍 ASP.NET Core 中的选项模式。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="a9ed4-112">若要了解如何在控制台应用中使用选项模式，请参阅 [.NET 中的选项模式](/dotnet/core/extensions/options)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="a9ed4-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a9ed4-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="a9ed4-114">绑定分层配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="a9ed4-115">选项接口</span><span class="sxs-lookup"><span data-stu-id="a9ed4-115">Options interfaces</span></span>

<span data-ttu-id="a9ed4-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="a9ed4-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="a9ed4-117">不支持：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-117">Does ***not*** support:</span></span>
  * <span data-ttu-id="a9ed4-118">在应用启动后读取配置数据。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-118">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="a9ed4-119">命名选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-119">Named options</span></span>](#named)
* <span data-ttu-id="a9ed4-120">注册为[单一实例](xref:fundamentals/dependency-injection#singleton)且可以注入到任何[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-120">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="a9ed4-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="a9ed4-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="a9ed4-122">在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-122">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="a9ed4-123">有关详细信息，请参阅[使用 IOptionsSnapshot 读取已更新的数据](#ios)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-123">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="a9ed4-124">注册为[范围内](xref:fundamentals/dependency-injection#scoped)，因此无法注入到单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-124">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="a9ed4-125">支持[命名选项](#named)</span><span class="sxs-lookup"><span data-stu-id="a9ed4-125">Supports [named options](#named)</span></span>

<span data-ttu-id="a9ed4-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="a9ed4-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="a9ed4-127">用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-127">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="a9ed4-128">注册为[单一实例](xref:fundamentals/dependency-injection#singleton)且可以注入到任何[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-128">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="a9ed4-129">支持：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-129">Supports:</span></span>
  * <span data-ttu-id="a9ed4-130">更改通知</span><span class="sxs-lookup"><span data-stu-id="a9ed4-130">Change notifications</span></span>
  * [<span data-ttu-id="a9ed4-131">命名选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-131">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="a9ed4-132">可重载配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-132">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="a9ed4-133">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="a9ed4-133">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="a9ed4-134">[后期配置](#options-post-configuration)方案允许在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-134">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="a9ed4-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="a9ed4-136">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-136">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="a9ed4-137">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-137">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="a9ed4-138">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-138">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="a9ed4-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="a9ed4-141">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-141">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="a9ed4-142">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="a9ed4-143">使用 IOptionsSnapshot 读取已更新的数据</span><span class="sxs-lookup"><span data-stu-id="a9ed4-143">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="a9ed4-144">通过使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>，针对请求生存期访问和缓存选项时，每个请求都会计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-144">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="a9ed4-145">当使用支持读取已更新的配置值的配置提供程序时，将在应用启动后读取对配置所做的更改。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-145">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="a9ed4-146">`IOptionsMonitor` 和 `IOptionsSnapshot` 之间的区别在于：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-146">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="a9ed4-147">`IOptionsMonitor` 是一种[单一示例服务](xref:fundamentals/dependency-injection#singleton)，可随时检索当前选项值，这在单一实例依赖项中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-147">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="a9ed4-148">`IOptionsSnapshot` 是一种[作用域服务](xref:fundamentals/dependency-injection#scoped)，并在构造 `IOptionsSnapshot<T>` 对象时提供选项的快照。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-148">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="a9ed4-149">选项快照旨在用于暂时性和有作用域的依赖项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-149">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="a9ed4-150">以下代码使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-150">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="a9ed4-151">以下代码注册 `MyOptions` 绑定的配置实例：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-151">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="a9ed4-152">在上面的代码中，已读取在应用启动后对 JSON 配置文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-152">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="a9ed4-153">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="a9ed4-153">IOptionsMonitor</span></span>

<span data-ttu-id="a9ed4-154">以下代码注册 `MyOptions` 绑定的配置实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-154">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="a9ed4-155">下面的示例使用 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-155">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="a9ed4-156">在上面的代码中，默认读取在应用启动后对 JSON 配置文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-156">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="a9ed4-157">命名选项支持使用 IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="a9ed4-157">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="a9ed4-158">命名选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-158">Named options:</span></span>

* <span data-ttu-id="a9ed4-159">当多个配置节绑定到同一属性时有用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-159">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="a9ed4-160">区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-160">Are case sensitive.</span></span>

<span data-ttu-id="a9ed4-161">请考虑使用以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-161">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="a9ed4-162">下面的类用于每个节，而不是创建两个类来绑定 `TopItem:Month` 和 `TopItem:Year`：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-162">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="a9ed4-163">下面的代码将配置命名选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-163">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="a9ed4-164">下面的代码将显示命名选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-164">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="a9ed4-165">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-165">All options are named instances.</span></span> <span data-ttu-id="a9ed4-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向 `Options.DefaultName` 实例，即 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="a9ed4-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="a9ed4-168"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-168">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="a9ed4-169">`null` 命名选项用于面向所有命名实例，而不是某一特定命名实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-169">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="a9ed4-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="a9ed4-171">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="a9ed4-171">OptionsBuilder API</span></span>

<span data-ttu-id="a9ed4-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-173">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-173">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="a9ed4-174">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-174">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="a9ed4-175">`OptionsBuilder` 在[选项验证](#val)部分中使用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-175">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="a9ed4-176">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-176">Use DI services to configure options</span></span>

<span data-ttu-id="a9ed4-177">在配置选项时，可以通过以下两种方式通过依赖关系注入访问服务：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-177">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="a9ed4-178">将配置委托传递给 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-178">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="a9ed4-179">`OptionsBuilder<TOptions>` 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-179">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="a9ed4-180">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-180">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="a9ed4-181">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-181">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="a9ed4-182">在调用 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 时，创建类型等效于框架执行的操作。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-182">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="a9ed4-183">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-183">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="a9ed4-184">选项验证</span><span class="sxs-lookup"><span data-stu-id="a9ed4-184">Options validation</span></span>

<span data-ttu-id="a9ed4-185">通过选项验证，可以验证选项值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-185">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="a9ed4-186">请考虑使用以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-186">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="a9ed4-187">下面的类绑定到 `"MyConfig"` 配置节，并应用若干 `DataAnnotations` 规则：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-187">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="a9ed4-188">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-188">The following code:</span></span>

* <span data-ttu-id="a9ed4-189">调用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> 以获取绑定到 `MyConfigOptions` 类的 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="a9ed4-190">调用 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> 以使用 `DataAnnotations` 启用验证。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-190">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="a9ed4-191">`ValidateDataAnnotations` 扩展方法在 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet 包中定义。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-191">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="a9ed4-192">对于使用 `Microsoft.NET.Sdk.Web` SDK 的 Web 应用，通过共享框架隐式引用此包。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-192">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="a9ed4-193">下面的代码显示配置值或验证错误：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-193">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="a9ed4-194">下面的代码使用委托应用更复杂的验证规则：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-194">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="a9ed4-195">用于复杂验证的 IValidateOptions</span><span class="sxs-lookup"><span data-stu-id="a9ed4-195">IValidateOptions for complex validation</span></span>

<span data-ttu-id="a9ed4-196">下面的类实现了 <xref:Microsoft.Extensions.Options.IValidateOptions`1>：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-196">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="a9ed4-197">`IValidateOptions` 允许将验证代码移出 `StartUp` 并将其移入类中。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-197">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="a9ed4-198">使用前面的代码，使用以下代码在 `Startup.ConfigureServices` 中启用验证：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-198">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="a9ed4-199">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-199">Options post-configuration</span></span>

<span data-ttu-id="a9ed4-200">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-200">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="a9ed4-201">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-201">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="a9ed4-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="a9ed4-203">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-203">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="a9ed4-204">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-204">Accessing options during startup</span></span>

<span data-ttu-id="a9ed4-205"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-205"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="a9ed4-206">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-206">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9ed4-207">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-207">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="a9ed4-208">ConfigurationExtensions NuGet 包</span><span class="sxs-lookup"><span data-stu-id="a9ed4-208">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="a9ed4-209">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包已在 ASP.NET Core 应用中隐式引用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-209">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a9ed4-210">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-210">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="a9ed4-211">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-211">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="a9ed4-212">[接口分隔原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-212">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="a9ed4-213">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)：应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-213">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="a9ed4-214">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-214">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="a9ed4-215">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-215">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="a9ed4-216">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a9ed4-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a9ed4-217">先决条件</span><span class="sxs-lookup"><span data-stu-id="a9ed4-217">Prerequisites</span></span>

<span data-ttu-id="a9ed4-218">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-218">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="a9ed4-219">选项接口</span><span class="sxs-lookup"><span data-stu-id="a9ed4-219">Options interfaces</span></span>

<span data-ttu-id="a9ed4-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="a9ed4-222">更改通知</span><span class="sxs-lookup"><span data-stu-id="a9ed4-222">Change notifications</span></span>
* [<span data-ttu-id="a9ed4-223">命名选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-223">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="a9ed4-224">可重载配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-224">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="a9ed4-225">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="a9ed4-225">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="a9ed4-226">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-226">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="a9ed4-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="a9ed4-228">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-228">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="a9ed4-229">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-229">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="a9ed4-230">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-230">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="a9ed4-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="a9ed4-233">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-233">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="a9ed4-234">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-234">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="a9ed4-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="a9ed4-236">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-236">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="a9ed4-237"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-237"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="a9ed4-238">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-238">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="a9ed4-239">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-239">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="a9ed4-240">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-240">General options configuration</span></span>

<span data-ttu-id="a9ed4-241">常规选项配置已作为示例 1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-241">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="a9ed4-242">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-242">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="a9ed4-243">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-243">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="a9ed4-244">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-244">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="a9ed4-245">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-245">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="a9ed4-246">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-246">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="a9ed4-247">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-247">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="a9ed4-248">示例的 appsettings.json 文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-248">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="a9ed4-249">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-249">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="a9ed4-250">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-250">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="a9ed4-251">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-251">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="a9ed4-252">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-252">Configure simple options with a delegate</span></span>

<span data-ttu-id="a9ed4-253">通过委托配置简单选项已作为示例 2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-253">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="a9ed4-254">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-254">Use a delegate to set options values.</span></span> <span data-ttu-id="a9ed4-255">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-255">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="a9ed4-256">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-256">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="a9ed4-257">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-257">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="a9ed4-258">Index.cshtml.cs:</span><span class="sxs-lookup"><span data-stu-id="a9ed4-258">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="a9ed4-259">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-259">You can add multiple configuration providers.</span></span> <span data-ttu-id="a9ed4-260">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-260">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="a9ed4-261">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-261">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="a9ed4-262">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-262">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="a9ed4-263">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json 中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-263">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="a9ed4-264">当启用多个配置服务时，指定的最后一个配置源优于其他源，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-264">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="a9ed4-265">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-265">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="a9ed4-266">子选项配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-266">Suboptions configuration</span></span>

<span data-ttu-id="a9ed4-267">子选项配置已作为示例 3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-267">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="a9ed4-268">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-268">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="a9ed4-269">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-269">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="a9ed4-270">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-270">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="a9ed4-271">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json 中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-271">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="a9ed4-272">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-272">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="a9ed4-273">它将 `MySubOptions` 绑定到 appsettings.json 文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-273">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="a9ed4-274">`GetSection` 方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-274">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="a9ed4-275">示例的 appsettings.json 文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-275">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="a9ed4-276">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-276">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="a9ed4-277">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-277">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="a9ed4-278">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-278">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="a9ed4-279">选项注入</span><span class="sxs-lookup"><span data-stu-id="a9ed4-279">Options injection</span></span>

<span data-ttu-id="a9ed4-280">选项注入已作为示例 4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-280">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="a9ed4-281">将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-281">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="a9ed4-282">使用 [`@inject`](xref:mvc/views/razor#inject) Razor 指令的 Razor 页面或 MVC 视图。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-282">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="a9ed4-283">页面或视图模型。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-283">A page or view model.</span></span>

<span data-ttu-id="a9ed4-284">示例应用中的以下示例将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入页面模型 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-284">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="a9ed4-285">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-285">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="a9ed4-286">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-286">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="a9ed4-288">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="a9ed4-288">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="a9ed4-289">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-289">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="a9ed4-290">通过使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>，针对请求生存期访问和缓存选项时，每个请求都会计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-290">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="a9ed4-291">`IOptionsMonitor` 和 `IOptionsSnapshot` 之间的区别在于：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-291">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="a9ed4-292">`IOptionsMonitor` 是一种[单一示例服务](xref:fundamentals/dependency-injection#singleton)，可随时检索当前选项值，这在单一实例依赖项中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-292">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="a9ed4-293">`IOptionsSnapshot` 是一种[作用域服务](xref:fundamentals/dependency-injection#scoped)，并在构造 `IOptionsSnapshot<T>` 对象时提供选项的快照。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-293">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="a9ed4-294">选项快照旨在用于暂时性和有作用域的依赖项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-294">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="a9ed4-295">以下示例演示如何在更改 appsettings.json (Pages/Index.cshtml.cs) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-295">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="a9ed4-296">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json 文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-296">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="a9ed4-297">下图显示从 appsettings.json 文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-297">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="a9ed4-298">将 appsettings.json 文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-298">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="a9ed4-299">保存 appsettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-299">Save the *appsettings.json* file.</span></span> <span data-ttu-id="a9ed4-300">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-300">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="a9ed4-301">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="a9ed4-301">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="a9ed4-302">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-302">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="a9ed4-303">命名选项支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-303">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="a9ed4-304">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-304">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="a9ed4-305">命名选项区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-305">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="a9ed4-306">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-306">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="a9ed4-307">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-307">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="a9ed4-308">从配置中提供从 appsettings.json 文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-308">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="a9ed4-309">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-309">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="a9ed4-310">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-310">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="a9ed4-311">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-311">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="a9ed4-312">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-312">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="a9ed4-313">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-313">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="a9ed4-314">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-314">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="a9ed4-315">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-315">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="a9ed4-316">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-316">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="a9ed4-317">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-317">All options are named instances.</span></span> <span data-ttu-id="a9ed4-318">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向 `Options.DefaultName` 实例，即 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-318">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="a9ed4-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="a9ed4-320"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-320">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="a9ed4-321">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-321">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="a9ed4-322">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="a9ed4-322">OptionsBuilder API</span></span>

<span data-ttu-id="a9ed4-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-324">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-324">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="a9ed4-325">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-325">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="a9ed4-326">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-326">Use DI services to configure options</span></span>

<span data-ttu-id="a9ed4-327">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-327">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="a9ed4-328">将配置委托传递给 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-328">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="a9ed4-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="a9ed4-330">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-330">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="a9ed4-331">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-331">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="a9ed4-332">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-332">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="a9ed4-333">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-333">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="a9ed4-334">选项验证</span><span class="sxs-lookup"><span data-stu-id="a9ed4-334">Options validation</span></span>

<span data-ttu-id="a9ed4-335">借助选项验证，可以验证已配置的选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-335">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="a9ed4-336">使用验证方法调用 `Validate`。如果选项有效，方法返回 `true`；如果无效，方法返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-336">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="a9ed4-337">上面的示例将命名选项实例设置为 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-337">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="a9ed4-338">默认选项实例为 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-338">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="a9ed4-339">选项验证在选项实例创建后运行。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-339">Validation runs when the options instance is created.</span></span> <span data-ttu-id="a9ed4-340">系统保证在选项实例首次获得访问时通过验证。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-340">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a9ed4-341">选项验证无法防止在创建选项实例后发生选项修改。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-341">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="a9ed4-342">例如，在首次访问选项时按请求创建并验证 `IOptionsSnapshot` 选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-342">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="a9ed4-343">对于同一个请求，在后续访问尝试时不会再次验证 `IOptionsSnapshot` 选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-343">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="a9ed4-344">`Validate` 方法接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-344">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="a9ed4-345">若要完全自定义验证，请实现 `IValidateOptions<TOptions>`，它支持：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-345">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="a9ed4-346">验证多种类型的选项：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="a9ed4-346">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="a9ed4-347">验证取决于其他选项类型：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="a9ed4-347">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="a9ed4-348">`IValidateOptions` 验证：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-348">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="a9ed4-349">特定的命名选项实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-349">A specific named options instance.</span></span>
* <span data-ttu-id="a9ed4-350">所有选项（如果 `name` 为 `null` 的话）。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-350">All options when `name` is `null`.</span></span>

<span data-ttu-id="a9ed4-351">通过接口实现返回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-351">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="a9ed4-352">通过调用 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，可以从 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 包中获得基于数据注释的验证。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-352">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="a9ed4-353">`Microsoft.Extensions.Options.DataAnnotations` 包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-353">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="a9ed4-354">设计团队正在考虑为未来版本提供预先验证（在启动时快速失败）。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-354">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="a9ed4-355">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-355">Options post-configuration</span></span>

<span data-ttu-id="a9ed4-356">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-356">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="a9ed4-357">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-357">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="a9ed4-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="a9ed4-359">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-359">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="a9ed4-360">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-360">Accessing options during startup</span></span>

<span data-ttu-id="a9ed4-361"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-361"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="a9ed4-362">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-362">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9ed4-363">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-363">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="a9ed4-364">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-364">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="a9ed4-365">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-365">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="a9ed4-366">[接口分隔原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation)：依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-366">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="a9ed4-367">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)：应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-367">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="a9ed4-368">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-368">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="a9ed4-369">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-369">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="a9ed4-370">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a9ed4-370">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a9ed4-371">先决条件</span><span class="sxs-lookup"><span data-stu-id="a9ed4-371">Prerequisites</span></span>

<span data-ttu-id="a9ed4-372">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-372">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="a9ed4-373">选项接口</span><span class="sxs-lookup"><span data-stu-id="a9ed4-373">Options interfaces</span></span>

<span data-ttu-id="a9ed4-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="a9ed4-376">更改通知</span><span class="sxs-lookup"><span data-stu-id="a9ed4-376">Change notifications</span></span>
* [<span data-ttu-id="a9ed4-377">命名选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-377">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="a9ed4-378">可重载配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-378">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="a9ed4-379">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="a9ed4-379">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="a9ed4-380">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-380">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="a9ed4-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="a9ed4-382">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-382">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="a9ed4-383">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-383">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="a9ed4-384">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-384">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="a9ed4-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="a9ed4-387">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-387">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="a9ed4-388">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-388">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="a9ed4-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="a9ed4-390">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-390">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="a9ed4-391"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-391"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="a9ed4-392">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-392">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="a9ed4-393">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-393">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="a9ed4-394">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-394">General options configuration</span></span>

<span data-ttu-id="a9ed4-395">常规选项配置已作为示例 1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-395">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="a9ed4-396">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-396">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="a9ed4-397">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-397">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="a9ed4-398">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-398">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="a9ed4-399">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-399">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="a9ed4-400">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-400">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="a9ed4-401">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-401">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="a9ed4-402">示例的 appsettings.json 文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-402">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="a9ed4-403">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-403">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="a9ed4-404">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-404">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="a9ed4-405">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-405">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="a9ed4-406">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-406">Configure simple options with a delegate</span></span>

<span data-ttu-id="a9ed4-407">通过委托配置简单选项已作为示例 2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-407">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="a9ed4-408">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-408">Use a delegate to set options values.</span></span> <span data-ttu-id="a9ed4-409">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-409">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="a9ed4-410">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-410">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="a9ed4-411">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-411">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="a9ed4-412">Index.cshtml.cs:</span><span class="sxs-lookup"><span data-stu-id="a9ed4-412">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="a9ed4-413">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-413">You can add multiple configuration providers.</span></span> <span data-ttu-id="a9ed4-414">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-414">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="a9ed4-415">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-415">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="a9ed4-416">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-416">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="a9ed4-417">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json 中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-417">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="a9ed4-418">当启用多个配置服务时，指定的最后一个配置源优于其他源，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-418">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="a9ed4-419">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-419">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="a9ed4-420">子选项配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-420">Suboptions configuration</span></span>

<span data-ttu-id="a9ed4-421">子选项配置已作为示例 3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-421">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="a9ed4-422">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-422">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="a9ed4-423">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-423">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="a9ed4-424">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-424">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="a9ed4-425">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json 中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-425">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="a9ed4-426">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-426">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="a9ed4-427">它将 `MySubOptions` 绑定到 appsettings.json 文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-427">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="a9ed4-428">`GetSection` 方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-428">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="a9ed4-429">示例的 appsettings.json 文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-429">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="a9ed4-430">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-430">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="a9ed4-431">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-431">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="a9ed4-432">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-432">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="a9ed4-433">视图模型或通过直接视图注入提供的选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-433">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="a9ed4-434">视图模型或通过直接视图注入提供的选项已作为示例 4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-434">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="a9ed4-435">可在视图模型中或通过将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接注入到视图 (Pages/Index.cshtml.cs) 来提供选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-435">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="a9ed4-436">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-436">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="a9ed4-437">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-437">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="a9ed4-439">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="a9ed4-439">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="a9ed4-440">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-440">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="a9ed4-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支持包含最小处理开销的重新加载选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="a9ed4-442">针对请求生存期访问和缓存选项时，每个请求只能计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-442">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="a9ed4-443">以下示例演示如何在更改 appsettings.json (Pages/Index.cshtml.cs) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-443">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="a9ed4-444">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json 文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-444">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="a9ed4-445">下图显示从 appsettings.json 文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-445">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="a9ed4-446">将 appsettings.json 文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-446">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="a9ed4-447">保存 appsettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-447">Save the *appsettings.json* file.</span></span> <span data-ttu-id="a9ed4-448">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-448">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="a9ed4-449">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="a9ed4-449">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="a9ed4-450">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-450">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="a9ed4-451">命名选项支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-451">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="a9ed4-452">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-452">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="a9ed4-453">命名选项区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-453">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="a9ed4-454">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-454">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="a9ed4-455">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-455">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="a9ed4-456">从配置中提供从 appsettings.json 文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-456">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="a9ed4-457">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-457">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="a9ed4-458">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-458">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="a9ed4-459">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-459">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="a9ed4-460">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-460">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="a9ed4-461">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-461">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="a9ed4-462">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-462">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="a9ed4-463">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-463">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="a9ed4-464">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-464">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="a9ed4-465">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-465">All options are named instances.</span></span> <span data-ttu-id="a9ed4-466">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向 `Options.DefaultName` 实例，即 `string.Empty`。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-466">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="a9ed4-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="a9ed4-468"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-468">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="a9ed4-469">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-469">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="a9ed4-470">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="a9ed4-470">OptionsBuilder API</span></span>

<span data-ttu-id="a9ed4-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="a9ed4-472">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-472">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="a9ed4-473">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-473">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="a9ed4-474">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-474">Use DI services to configure options</span></span>

<span data-ttu-id="a9ed4-475">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-475">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="a9ed4-476">将配置委托传递给 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-476">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="a9ed4-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="a9ed4-478">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-478">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="a9ed4-479">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-479">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="a9ed4-480">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-480">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="a9ed4-481">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-481">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="a9ed4-482">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="a9ed4-482">Options post-configuration</span></span>

<span data-ttu-id="a9ed4-483">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-483">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="a9ed4-484">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-484">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="a9ed4-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="a9ed4-486">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="a9ed4-486">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="a9ed4-487">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="a9ed4-487">Accessing options during startup</span></span>

<span data-ttu-id="a9ed4-488"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-488"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="a9ed4-489">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-489">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9ed4-490">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="a9ed4-490">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a9ed4-491">其他资源</span><span class="sxs-lookup"><span data-stu-id="a9ed4-491">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
