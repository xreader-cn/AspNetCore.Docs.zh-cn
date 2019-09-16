---
title: 使用 ASP.NET Core 中的更改令牌检测更改
author: guardrex
description: 了解如何使用更改令牌来跟踪更改。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/27/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 86cde7b60f5c398fc6bb215b593643c05565cf3c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384716"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="f0ad1-103">使用 ASP.NET Core 中的更改令牌检测更改</span><span class="sxs-lookup"><span data-stu-id="f0ad1-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="f0ad1-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f0ad1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0ad1-105">更改令牌  是用于跟踪状态更改的通用、低级别构建基块。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="f0ad1-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f0ad1-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="f0ad1-107">IChangeToken 接口</span><span class="sxs-lookup"><span data-stu-id="f0ad1-107">IChangeToken interface</span></span>

<span data-ttu-id="f0ad1-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="f0ad1-109">`IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f0ad1-110">将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-110">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="f0ad1-111">`IChangeToken` 具有以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="f0ad1-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="f0ad1-113">如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="f0ad1-114">如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="f0ad1-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="f0ad1-116">`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="f0ad1-117">调用回调之前，必须设置 `HasChanged`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="f0ad1-118">ChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="f0ad1-118">ChangeToken class</span></span>

<span data-ttu-id="f0ad1-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="f0ad1-120">`ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f0ad1-121">将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-121">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="f0ad1-122">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="f0ad1-123">`Func<IChangeToken>` 生成令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="f0ad1-124">令牌更改时，调用 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="f0ad1-125">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="f0ad1-126">`OnChange` 返回 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="f0ad1-127">调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="f0ad1-128">ASP.NET Core 中更改令牌的使用示例</span><span class="sxs-lookup"><span data-stu-id="f0ad1-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="f0ad1-129">更改令牌主要用于在 ASP.NET Core 中监视对象更改：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="f0ad1-130">为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="f0ad1-131">可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="f0ad1-132">对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="f0ad1-133">每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="f0ad1-134">监视配置更改</span><span class="sxs-lookup"><span data-stu-id="f0ad1-134">Monitor for configuration changes</span></span>

<span data-ttu-id="f0ad1-135">默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json  、appsettings.Development.json  和 appsettings.Production.json  ）来加载应用配置设置。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="f0ad1-136">使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="f0ad1-137">`reloadOnChange` 指示文件更改时是否应该重载配置。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="f0ad1-138">此设置出现在 <xref:Microsoft.Extensions.Hosting.Host> 便捷方法 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-138">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="f0ad1-139">基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="f0ad1-140">`FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="f0ad1-141">默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="f0ad1-142">示例应用演示监视配置更改的两个实现。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="f0ad1-143">如果任一 appsettings  文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="f0ad1-144">配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="f0ad1-145">为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="f0ad1-146">此示例使用 SHA1 文件哈希。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="f0ad1-147">使用指数回退实现重试。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="f0ad1-148">进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="f0ad1-149">Utilities/Utilities.cs  ：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="f0ad1-150">简单启动更改令牌</span><span class="sxs-lookup"><span data-stu-id="f0ad1-150">Simple startup change token</span></span>

<span data-ttu-id="f0ad1-151">将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="f0ad1-152">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f0ad1-153">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="f0ad1-154">回调是 `InvokeChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f0ad1-155">回调的 `state` 用于在 `IWebHostEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings  配置文件（例如开发环境中的 appsettings.Development.json  ）。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-155">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="f0ad1-156">只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="f0ad1-157">只要应用正在运行，该系统就会运行，并且用户不能禁用。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="f0ad1-158">将配置更改作为服务进行监视</span><span class="sxs-lookup"><span data-stu-id="f0ad1-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="f0ad1-159">示例实现：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-159">The sample implements:</span></span>

* <span data-ttu-id="f0ad1-160">基本启动令牌监视。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="f0ad1-161">作为服务监视。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-161">Monitoring as a service.</span></span>
* <span data-ttu-id="f0ad1-162">启用和禁用监视的机制。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="f0ad1-163">示例建立 `IConfigurationMonitor` 接口。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="f0ad1-164">Extensions/ConfigurationMonitor.cs  ：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="f0ad1-165">已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="f0ad1-166">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="f0ad1-167">`InvokeChanged` 是回调方法。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="f0ad1-168">此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="f0ad1-169">使用了以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-169">Two properties are used:</span></span>

* <span data-ttu-id="f0ad1-170">`MonitoringEnabled` &ndash; 指示回调是否应该运行其自定义代码。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="f0ad1-171">`CurrentState` &ndash; 描述在 UI 中使用的当前监视状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="f0ad1-172">`InvokeChanged` 方法类似于前面的方法，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="f0ad1-173">不运行其代码，除非 `MonitoringEnabled` 为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="f0ad1-174">输出其 `WriteConsole` 输出中的当前 `state`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="f0ad1-175">将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f0ad1-176">索引页提供对配置监视的用户控制。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="f0ad1-177">将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="f0ad1-178"> Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f0ad1-179">配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="f0ad1-180">触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="f0ad1-181">触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="f0ad1-182">UI 启用和禁用监视的按钮。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="f0ad1-183"> Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="f0ad1-184">监视缓存文件更改</span><span class="sxs-lookup"><span data-stu-id="f0ad1-184">Monitor cached file changes</span></span>

<span data-ttu-id="f0ad1-185">可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="f0ad1-186">[内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="f0ad1-187">无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧  （过时）数据。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="f0ad1-188">例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="f0ad1-189">数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="f0ad1-190">任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="f0ad1-191">在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="f0ad1-192">示例应用演示方法的实现。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="f0ad1-193">示例使用 `GetFileContent` 来完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="f0ad1-194">返回文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-194">Return file content.</span></span>
* <span data-ttu-id="f0ad1-195">实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="f0ad1-196">Utilities/Utilities.cs  ：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="f0ad1-197">创建 `FileService` 以处理缓存文件查找。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="f0ad1-198">服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs  )。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="f0ad1-199">如果使用缓存键未找到缓存的内容，则将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="f0ad1-200">使用 `GetFileContent` 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="f0ad1-201">使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="f0ad1-202">修改该文件时，会触发令牌的回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="f0ad1-203">[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="f0ad1-204">如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="f0ad1-205">在下面的示例中，文件存储在应用的内容根目录中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-205">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="f0ad1-206">`IWebHostEnvironment.ContentRootFileProvider` 用于获取指向应用的 `IWebHostEnvironment.ContentRootPath` 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-206">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="f0ad1-207">`filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="f0ad1-208">将 `FileService` 与内存缓存服务一起注册在服务容器中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="f0ad1-209">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f0ad1-210">页面模型使用服务加载文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="f0ad1-211">在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs  ) 中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="f0ad1-212">CompositeChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="f0ad1-212">CompositeChangeToken class</span></span>

<span data-ttu-id="f0ad1-213">要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="f0ad1-214">如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="f0ad1-215">如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="f0ad1-216">如果发生多个并发更改事件，则调用一次复合更改回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0ad1-217">更改令牌  是用于跟踪状态更改的通用、低级别构建基块。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-217">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="f0ad1-218">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f0ad1-218">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="f0ad1-219">IChangeToken 接口</span><span class="sxs-lookup"><span data-stu-id="f0ad1-219">IChangeToken interface</span></span>

<span data-ttu-id="f0ad1-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="f0ad1-221">`IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-221">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f0ad1-222">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-222">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="f0ad1-223">`IChangeToken` 具有以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-223">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="f0ad1-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="f0ad1-225">如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-225">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="f0ad1-226">如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-226">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="f0ad1-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="f0ad1-228">`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-228">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="f0ad1-229">调用回调之前，必须设置 `HasChanged`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-229">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="f0ad1-230">ChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="f0ad1-230">ChangeToken class</span></span>

<span data-ttu-id="f0ad1-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="f0ad1-232">`ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-232">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="f0ad1-233">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-233">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="f0ad1-234">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-234">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="f0ad1-235">`Func<IChangeToken>` 生成令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-235">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="f0ad1-236">令牌更改时，调用 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-236">`Action` is called when the token changes.</span></span>

<span data-ttu-id="f0ad1-237">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-237">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="f0ad1-238">`OnChange` 返回 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-238">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="f0ad1-239">调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-239">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="f0ad1-240">ASP.NET Core 中更改令牌的使用示例</span><span class="sxs-lookup"><span data-stu-id="f0ad1-240">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="f0ad1-241">更改令牌主要用于在 ASP.NET Core 中监视对象更改：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-241">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="f0ad1-242">为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-242">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="f0ad1-243">可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-243">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="f0ad1-244">对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-244">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="f0ad1-245">每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-245">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="f0ad1-246">监视配置更改</span><span class="sxs-lookup"><span data-stu-id="f0ad1-246">Monitor for configuration changes</span></span>

<span data-ttu-id="f0ad1-247">默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json  、appsettings.Development.json  和 appsettings.Production.json  ）来加载应用配置设置。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-247">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="f0ad1-248">使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-248">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="f0ad1-249">`reloadOnChange` 指示文件更改时是否应该重载配置。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-249">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="f0ad1-250">此设置出现在 <xref:Microsoft.AspNetCore.WebHost> 便捷方法 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-250">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="f0ad1-251">基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-251">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="f0ad1-252">`FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-252">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="f0ad1-253">默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-253">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="f0ad1-254">示例应用演示监视配置更改的两个实现。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-254">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="f0ad1-255">如果任一 appsettings  文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-255">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="f0ad1-256">配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-256">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="f0ad1-257">为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-257">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="f0ad1-258">此示例使用 SHA1 文件哈希。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-258">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="f0ad1-259">使用指数回退实现重试。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-259">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="f0ad1-260">进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-260">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="f0ad1-261">Utilities/Utilities.cs  ：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-261">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="f0ad1-262">简单启动更改令牌</span><span class="sxs-lookup"><span data-stu-id="f0ad1-262">Simple startup change token</span></span>

<span data-ttu-id="f0ad1-263">将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-263">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="f0ad1-264">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-264">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f0ad1-265">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-265">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="f0ad1-266">回调是 `InvokeChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-266">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f0ad1-267">回调的 `state` 用于在 `IHostingEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings  配置文件（例如开发环境中的 appsettings.Development.json  ）。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-267">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="f0ad1-268">只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-268">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="f0ad1-269">只要应用正在运行，该系统就会运行，并且用户不能禁用。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-269">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="f0ad1-270">将配置更改作为服务进行监视</span><span class="sxs-lookup"><span data-stu-id="f0ad1-270">Monitor configuration changes as a service</span></span>

<span data-ttu-id="f0ad1-271">示例实现：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-271">The sample implements:</span></span>

* <span data-ttu-id="f0ad1-272">基本启动令牌监视。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-272">Basic startup token monitoring.</span></span>
* <span data-ttu-id="f0ad1-273">作为服务监视。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-273">Monitoring as a service.</span></span>
* <span data-ttu-id="f0ad1-274">启用和禁用监视的机制。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-274">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="f0ad1-275">示例建立 `IConfigurationMonitor` 接口。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-275">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="f0ad1-276">Extensions/ConfigurationMonitor.cs  ：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-276">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="f0ad1-277">已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-277">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="f0ad1-278">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-278">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="f0ad1-279">`InvokeChanged` 是回调方法。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-279">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="f0ad1-280">此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-280">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="f0ad1-281">使用了以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-281">Two properties are used:</span></span>

* <span data-ttu-id="f0ad1-282">`MonitoringEnabled` &ndash; 指示回调是否应该运行其自定义代码。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-282">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="f0ad1-283">`CurrentState` &ndash; 描述在 UI 中使用的当前监视状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-283">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="f0ad1-284">`InvokeChanged` 方法类似于前面的方法，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-284">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="f0ad1-285">不运行其代码，除非 `MonitoringEnabled` 为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-285">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="f0ad1-286">输出其 `WriteConsole` 输出中的当前 `state`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-286">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="f0ad1-287">将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-287">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f0ad1-288">索引页提供对配置监视的用户控制。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-288">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="f0ad1-289">将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-289">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="f0ad1-290"> Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-290">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f0ad1-291">配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-291">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="f0ad1-292">触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-292">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="f0ad1-293">触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-293">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="f0ad1-294">UI 启用和禁用监视的按钮。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-294">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="f0ad1-295"> Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-295">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="f0ad1-296">监视缓存文件更改</span><span class="sxs-lookup"><span data-stu-id="f0ad1-296">Monitor cached file changes</span></span>

<span data-ttu-id="f0ad1-297">可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-297">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="f0ad1-298">[内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-298">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="f0ad1-299">无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧  （过时）数据。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-299">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="f0ad1-300">例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-300">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="f0ad1-301">数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-301">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="f0ad1-302">任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-302">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="f0ad1-303">在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-303">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="f0ad1-304">示例应用演示方法的实现。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-304">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="f0ad1-305">示例使用 `GetFileContent` 来完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-305">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="f0ad1-306">返回文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-306">Return file content.</span></span>
* <span data-ttu-id="f0ad1-307">实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-307">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="f0ad1-308">Utilities/Utilities.cs  ：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-308">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="f0ad1-309">创建 `FileService` 以处理缓存文件查找。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-309">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="f0ad1-310">服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs  )。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-310">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="f0ad1-311">如果使用缓存键未找到缓存的内容，则将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-311">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="f0ad1-312">使用 `GetFileContent` 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-312">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="f0ad1-313">使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-313">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="f0ad1-314">修改该文件时，会触发令牌的回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-314">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="f0ad1-315">[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-315">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="f0ad1-316">如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-316">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="f0ad1-317">在下面的示例中，文件存储在应用的内容根目录中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-317">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="f0ad1-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) 用于获取指向应用的 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="f0ad1-319">`filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-319">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="f0ad1-320">将 `FileService` 与内存缓存服务一起注册在服务容器中。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-320">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="f0ad1-321">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-321">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f0ad1-322">页面模型使用服务加载文件内容。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-322">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="f0ad1-323">在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs  ) 中：</span><span class="sxs-lookup"><span data-stu-id="f0ad1-323">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="f0ad1-324">CompositeChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="f0ad1-324">CompositeChangeToken class</span></span>

<span data-ttu-id="f0ad1-325">要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-325">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="f0ad1-326">如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-326">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="f0ad1-327">如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-327">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="f0ad1-328">如果发生多个并发更改事件，则调用一次复合更改回调。</span><span class="sxs-lookup"><span data-stu-id="f0ad1-328">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f0ad1-329">其他资源</span><span class="sxs-lookup"><span data-stu-id="f0ad1-329">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
