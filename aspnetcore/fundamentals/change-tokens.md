---
title: 使用 ASP.NET Core 中的更改令牌检测更改
author: rick-anderson
description: 了解如何使用更改令牌来跟踪更改。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: f20d44c7767b284f727ce19a46224dae0cf6a5e1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053769"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="370c9-103">使用 ASP.NET Core 中的更改令牌检测更改</span><span class="sxs-lookup"><span data-stu-id="370c9-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="370c9-104">更改令牌是用于跟踪状态更改的通用、低级别构建基块。</span><span class="sxs-lookup"><span data-stu-id="370c9-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="370c9-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="370c9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="370c9-106">IChangeToken 接口</span><span class="sxs-lookup"><span data-stu-id="370c9-106">IChangeToken interface</span></span>

<span data-ttu-id="370c9-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="370c9-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="370c9-108">`IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="370c9-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="370c9-109">将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="370c9-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="370c9-110">`IChangeToken` 具有以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="370c9-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="370c9-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="370c9-112">如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。</span><span class="sxs-lookup"><span data-stu-id="370c9-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="370c9-113">如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="370c9-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。</span><span class="sxs-lookup"><span data-stu-id="370c9-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="370c9-115">`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="370c9-116">调用回调之前，必须设置 `HasChanged`。</span><span class="sxs-lookup"><span data-stu-id="370c9-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="370c9-117">ChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="370c9-117">ChangeToken class</span></span>

<span data-ttu-id="370c9-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="370c9-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="370c9-119">`ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="370c9-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="370c9-120">将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="370c9-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="370c9-121">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：</span><span class="sxs-lookup"><span data-stu-id="370c9-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="370c9-122">`Func<IChangeToken>` 生成令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="370c9-123">令牌更改时，调用 `Action`。</span><span class="sxs-lookup"><span data-stu-id="370c9-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="370c9-124">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。</span><span class="sxs-lookup"><span data-stu-id="370c9-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="370c9-125">`OnChange` 返回 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="370c9-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="370c9-126">调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。</span><span class="sxs-lookup"><span data-stu-id="370c9-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="370c9-127">ASP.NET Core 中更改令牌的使用示例</span><span class="sxs-lookup"><span data-stu-id="370c9-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="370c9-128">更改令牌主要用于在 ASP.NET Core 中监视对象更改：</span><span class="sxs-lookup"><span data-stu-id="370c9-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="370c9-129">为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。</span><span class="sxs-lookup"><span data-stu-id="370c9-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="370c9-130">可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。</span><span class="sxs-lookup"><span data-stu-id="370c9-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="370c9-131">对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。</span><span class="sxs-lookup"><span data-stu-id="370c9-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="370c9-132">每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="370c9-133">监视配置更改</span><span class="sxs-lookup"><span data-stu-id="370c9-133">Monitor for configuration changes</span></span>

<span data-ttu-id="370c9-134">默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json、appsettings.Development.json 和 appsettings.Production.json）来加载应用配置设置。</span><span class="sxs-lookup"><span data-stu-id="370c9-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="370c9-135">使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。</span><span class="sxs-lookup"><span data-stu-id="370c9-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="370c9-136">`reloadOnChange` 指示文件更改时是否应该重载配置。</span><span class="sxs-lookup"><span data-stu-id="370c9-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="370c9-137">此设置出现在 <xref:Microsoft.Extensions.Hosting.Host> 便捷方法 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 中：</span><span class="sxs-lookup"><span data-stu-id="370c9-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="370c9-138">基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。</span><span class="sxs-lookup"><span data-stu-id="370c9-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="370c9-139">`FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。</span><span class="sxs-lookup"><span data-stu-id="370c9-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="370c9-140">默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。</span><span class="sxs-lookup"><span data-stu-id="370c9-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="370c9-141">示例应用演示监视配置更改的两个实现。</span><span class="sxs-lookup"><span data-stu-id="370c9-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="370c9-142">如果任一 appsettings 文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。</span><span class="sxs-lookup"><span data-stu-id="370c9-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="370c9-143">配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。</span><span class="sxs-lookup"><span data-stu-id="370c9-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="370c9-144">为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。</span><span class="sxs-lookup"><span data-stu-id="370c9-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="370c9-145">此示例使用 SHA1 文件哈希。</span><span class="sxs-lookup"><span data-stu-id="370c9-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="370c9-146">使用指数回退实现重试。</span><span class="sxs-lookup"><span data-stu-id="370c9-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="370c9-147">进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。</span><span class="sxs-lookup"><span data-stu-id="370c9-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="370c9-148">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-148">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="370c9-149">简单启动更改令牌</span><span class="sxs-lookup"><span data-stu-id="370c9-149">Simple startup change token</span></span>

<span data-ttu-id="370c9-150">将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="370c9-151">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="370c9-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="370c9-152">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="370c9-153">回调是 `InvokeChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="370c9-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="370c9-154">回调的 `state` 用于在 `IWebHostEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings 配置文件（例如开发环境中的 appsettings.Development.json）。</span><span class="sxs-lookup"><span data-stu-id="370c9-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="370c9-155">只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。</span><span class="sxs-lookup"><span data-stu-id="370c9-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="370c9-156">只要应用正在运行，该系统就会运行，并且用户不能禁用。</span><span class="sxs-lookup"><span data-stu-id="370c9-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="370c9-157">将配置更改作为服务进行监视</span><span class="sxs-lookup"><span data-stu-id="370c9-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="370c9-158">示例实现：</span><span class="sxs-lookup"><span data-stu-id="370c9-158">The sample implements:</span></span>

* <span data-ttu-id="370c9-159">基本启动令牌监视。</span><span class="sxs-lookup"><span data-stu-id="370c9-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="370c9-160">作为服务监视。</span><span class="sxs-lookup"><span data-stu-id="370c9-160">Monitoring as a service.</span></span>
* <span data-ttu-id="370c9-161">启用和禁用监视的机制。</span><span class="sxs-lookup"><span data-stu-id="370c9-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="370c9-162">示例建立 `IConfigurationMonitor` 接口。</span><span class="sxs-lookup"><span data-stu-id="370c9-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="370c9-163">Extensions/ConfigurationMonitor.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-163">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="370c9-164">已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：</span><span class="sxs-lookup"><span data-stu-id="370c9-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="370c9-165">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="370c9-166">`InvokeChanged` 是回调方法。</span><span class="sxs-lookup"><span data-stu-id="370c9-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="370c9-167">此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="370c9-168">使用了以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="370c9-168">Two properties are used:</span></span>

* <span data-ttu-id="370c9-169">`MonitoringEnabled`：指示回调是否应该运行其自定义代码。</span><span class="sxs-lookup"><span data-stu-id="370c9-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="370c9-170">`CurrentState`：描述在 UI 中使用的当前监视状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="370c9-171">`InvokeChanged` 方法类似于前面的方法，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="370c9-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="370c9-172">不运行其代码，除非 `MonitoringEnabled` 为 `true`。</span><span class="sxs-lookup"><span data-stu-id="370c9-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="370c9-173">输出其 `WriteConsole` 输出中的当前 `state`。</span><span class="sxs-lookup"><span data-stu-id="370c9-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="370c9-174">将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：</span><span class="sxs-lookup"><span data-stu-id="370c9-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="370c9-175">索引页提供对配置监视的用户控制。</span><span class="sxs-lookup"><span data-stu-id="370c9-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="370c9-176">将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。</span><span class="sxs-lookup"><span data-stu-id="370c9-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="370c9-177">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-177">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="370c9-178">配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：</span><span class="sxs-lookup"><span data-stu-id="370c9-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="370c9-179">触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="370c9-180">触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。</span><span class="sxs-lookup"><span data-stu-id="370c9-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="370c9-181">UI 启用和禁用监视的按钮。</span><span class="sxs-lookup"><span data-stu-id="370c9-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="370c9-182">Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="370c9-182">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="370c9-183">监视缓存文件更改</span><span class="sxs-lookup"><span data-stu-id="370c9-183">Monitor cached file changes</span></span>

<span data-ttu-id="370c9-184">可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="370c9-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="370c9-185">[内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="370c9-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="370c9-186">无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧（过时）数据。</span><span class="sxs-lookup"><span data-stu-id="370c9-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="370c9-187">例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="370c9-188">数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。</span><span class="sxs-lookup"><span data-stu-id="370c9-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="370c9-189">任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="370c9-190">在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="370c9-191">示例应用演示方法的实现。</span><span class="sxs-lookup"><span data-stu-id="370c9-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="370c9-192">示例使用 `GetFileContent` 来完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="370c9-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="370c9-193">返回文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-193">Return file content.</span></span>
* <span data-ttu-id="370c9-194">实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。</span><span class="sxs-lookup"><span data-stu-id="370c9-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="370c9-195">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-195">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="370c9-196">创建 `FileService` 以处理缓存文件查找。</span><span class="sxs-lookup"><span data-stu-id="370c9-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="370c9-197">服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs)。</span><span class="sxs-lookup"><span data-stu-id="370c9-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="370c9-198">如果使用缓存键未找到缓存的内容，则将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="370c9-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="370c9-199">使用 `GetFileContent` 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="370c9-200">使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="370c9-201">修改该文件时，会触发令牌的回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="370c9-202">[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="370c9-203">如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。</span><span class="sxs-lookup"><span data-stu-id="370c9-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="370c9-204">在下面的示例中，文件存储在应用的[内容根目录](xref:fundamentals/index#content-root)中。</span><span class="sxs-lookup"><span data-stu-id="370c9-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="370c9-205">`IWebHostEnvironment.ContentRootFileProvider` 用于获取指向应用的 `IWebHostEnvironment.ContentRootPath` 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="370c9-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="370c9-206">`filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。</span><span class="sxs-lookup"><span data-stu-id="370c9-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="370c9-207">将 `FileService` 与内存缓存服务一起注册在服务容器中。</span><span class="sxs-lookup"><span data-stu-id="370c9-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="370c9-208">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="370c9-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="370c9-209">页面模型使用服务加载文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="370c9-210">在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs) 中：</span><span class="sxs-lookup"><span data-stu-id="370c9-210">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="370c9-211">CompositeChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="370c9-211">CompositeChangeToken class</span></span>

<span data-ttu-id="370c9-212">要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。</span><span class="sxs-lookup"><span data-stu-id="370c9-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="370c9-213">如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="370c9-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="370c9-214">如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="370c9-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="370c9-215">如果发生多个并发更改事件，则调用一次复合更改回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="370c9-216">更改令牌是用于跟踪状态更改的通用、低级别构建基块。</span><span class="sxs-lookup"><span data-stu-id="370c9-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="370c9-217">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="370c9-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="370c9-218">IChangeToken 接口</span><span class="sxs-lookup"><span data-stu-id="370c9-218">IChangeToken interface</span></span>

<span data-ttu-id="370c9-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="370c9-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="370c9-220">`IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="370c9-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="370c9-221">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。</span><span class="sxs-lookup"><span data-stu-id="370c9-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="370c9-222">`IChangeToken` 具有以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="370c9-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="370c9-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="370c9-224">如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。</span><span class="sxs-lookup"><span data-stu-id="370c9-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="370c9-225">如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="370c9-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。</span><span class="sxs-lookup"><span data-stu-id="370c9-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="370c9-227">`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="370c9-228">调用回调之前，必须设置 `HasChanged`。</span><span class="sxs-lookup"><span data-stu-id="370c9-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="370c9-229">ChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="370c9-229">ChangeToken class</span></span>

<span data-ttu-id="370c9-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="370c9-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="370c9-231">`ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="370c9-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="370c9-232">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。</span><span class="sxs-lookup"><span data-stu-id="370c9-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="370c9-233">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：</span><span class="sxs-lookup"><span data-stu-id="370c9-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="370c9-234">`Func<IChangeToken>` 生成令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="370c9-235">令牌更改时，调用 `Action`。</span><span class="sxs-lookup"><span data-stu-id="370c9-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="370c9-236">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。</span><span class="sxs-lookup"><span data-stu-id="370c9-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="370c9-237">`OnChange` 返回 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="370c9-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="370c9-238">调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。</span><span class="sxs-lookup"><span data-stu-id="370c9-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="370c9-239">ASP.NET Core 中更改令牌的使用示例</span><span class="sxs-lookup"><span data-stu-id="370c9-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="370c9-240">更改令牌主要用于在 ASP.NET Core 中监视对象更改：</span><span class="sxs-lookup"><span data-stu-id="370c9-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="370c9-241">为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。</span><span class="sxs-lookup"><span data-stu-id="370c9-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="370c9-242">可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。</span><span class="sxs-lookup"><span data-stu-id="370c9-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="370c9-243">对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。</span><span class="sxs-lookup"><span data-stu-id="370c9-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="370c9-244">每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="370c9-245">监视配置更改</span><span class="sxs-lookup"><span data-stu-id="370c9-245">Monitor for configuration changes</span></span>

<span data-ttu-id="370c9-246">默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json、appsettings.Development.json 和 appsettings.Production.json）来加载应用配置设置。</span><span class="sxs-lookup"><span data-stu-id="370c9-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="370c9-247">使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。</span><span class="sxs-lookup"><span data-stu-id="370c9-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="370c9-248">`reloadOnChange` 指示文件更改时是否应该重载配置。</span><span class="sxs-lookup"><span data-stu-id="370c9-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="370c9-249">此设置出现在 <xref:Microsoft.AspNetCore.WebHost> 便捷方法 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 中：</span><span class="sxs-lookup"><span data-stu-id="370c9-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="370c9-250">基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。</span><span class="sxs-lookup"><span data-stu-id="370c9-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="370c9-251">`FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。</span><span class="sxs-lookup"><span data-stu-id="370c9-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="370c9-252">默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。</span><span class="sxs-lookup"><span data-stu-id="370c9-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="370c9-253">示例应用演示监视配置更改的两个实现。</span><span class="sxs-lookup"><span data-stu-id="370c9-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="370c9-254">如果任一 appsettings 文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。</span><span class="sxs-lookup"><span data-stu-id="370c9-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="370c9-255">配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。</span><span class="sxs-lookup"><span data-stu-id="370c9-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="370c9-256">为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。</span><span class="sxs-lookup"><span data-stu-id="370c9-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="370c9-257">此示例使用 SHA1 文件哈希。</span><span class="sxs-lookup"><span data-stu-id="370c9-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="370c9-258">使用指数回退实现重试。</span><span class="sxs-lookup"><span data-stu-id="370c9-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="370c9-259">进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。</span><span class="sxs-lookup"><span data-stu-id="370c9-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="370c9-260">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-260">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="370c9-261">简单启动更改令牌</span><span class="sxs-lookup"><span data-stu-id="370c9-261">Simple startup change token</span></span>

<span data-ttu-id="370c9-262">将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="370c9-263">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="370c9-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="370c9-264">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="370c9-265">回调是 `InvokeChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="370c9-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="370c9-266">回调的 `state` 用于在 `IHostingEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings 配置文件（例如开发环境中的 appsettings.Development.json）。</span><span class="sxs-lookup"><span data-stu-id="370c9-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="370c9-267">只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。</span><span class="sxs-lookup"><span data-stu-id="370c9-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="370c9-268">只要应用正在运行，该系统就会运行，并且用户不能禁用。</span><span class="sxs-lookup"><span data-stu-id="370c9-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="370c9-269">将配置更改作为服务进行监视</span><span class="sxs-lookup"><span data-stu-id="370c9-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="370c9-270">示例实现：</span><span class="sxs-lookup"><span data-stu-id="370c9-270">The sample implements:</span></span>

* <span data-ttu-id="370c9-271">基本启动令牌监视。</span><span class="sxs-lookup"><span data-stu-id="370c9-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="370c9-272">作为服务监视。</span><span class="sxs-lookup"><span data-stu-id="370c9-272">Monitoring as a service.</span></span>
* <span data-ttu-id="370c9-273">启用和禁用监视的机制。</span><span class="sxs-lookup"><span data-stu-id="370c9-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="370c9-274">示例建立 `IConfigurationMonitor` 接口。</span><span class="sxs-lookup"><span data-stu-id="370c9-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="370c9-275">Extensions/ConfigurationMonitor.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-275">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="370c9-276">已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：</span><span class="sxs-lookup"><span data-stu-id="370c9-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="370c9-277">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="370c9-278">`InvokeChanged` 是回调方法。</span><span class="sxs-lookup"><span data-stu-id="370c9-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="370c9-279">此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="370c9-280">使用了以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="370c9-280">Two properties are used:</span></span>

* <span data-ttu-id="370c9-281">`MonitoringEnabled`：指示回调是否应该运行其自定义代码。</span><span class="sxs-lookup"><span data-stu-id="370c9-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="370c9-282">`CurrentState`：描述在 UI 中使用的当前监视状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="370c9-283">`InvokeChanged` 方法类似于前面的方法，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="370c9-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="370c9-284">不运行其代码，除非 `MonitoringEnabled` 为 `true`。</span><span class="sxs-lookup"><span data-stu-id="370c9-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="370c9-285">输出其 `WriteConsole` 输出中的当前 `state`。</span><span class="sxs-lookup"><span data-stu-id="370c9-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="370c9-286">将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：</span><span class="sxs-lookup"><span data-stu-id="370c9-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="370c9-287">索引页提供对配置监视的用户控制。</span><span class="sxs-lookup"><span data-stu-id="370c9-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="370c9-288">将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。</span><span class="sxs-lookup"><span data-stu-id="370c9-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="370c9-289">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-289">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="370c9-290">配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：</span><span class="sxs-lookup"><span data-stu-id="370c9-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="370c9-291">触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="370c9-292">触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。</span><span class="sxs-lookup"><span data-stu-id="370c9-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="370c9-293">UI 启用和禁用监视的按钮。</span><span class="sxs-lookup"><span data-stu-id="370c9-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="370c9-294">Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="370c9-294">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="370c9-295">监视缓存文件更改</span><span class="sxs-lookup"><span data-stu-id="370c9-295">Monitor cached file changes</span></span>

<span data-ttu-id="370c9-296">可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="370c9-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="370c9-297">[内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="370c9-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="370c9-298">无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧（过时）数据。</span><span class="sxs-lookup"><span data-stu-id="370c9-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="370c9-299">例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。</span><span class="sxs-lookup"><span data-stu-id="370c9-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="370c9-300">数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。</span><span class="sxs-lookup"><span data-stu-id="370c9-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="370c9-301">任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="370c9-302">在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="370c9-303">示例应用演示方法的实现。</span><span class="sxs-lookup"><span data-stu-id="370c9-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="370c9-304">示例使用 `GetFileContent` 来完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="370c9-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="370c9-305">返回文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-305">Return file content.</span></span>
* <span data-ttu-id="370c9-306">实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。</span><span class="sxs-lookup"><span data-stu-id="370c9-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="370c9-307">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="370c9-307">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="370c9-308">创建 `FileService` 以处理缓存文件查找。</span><span class="sxs-lookup"><span data-stu-id="370c9-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="370c9-309">服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs)。</span><span class="sxs-lookup"><span data-stu-id="370c9-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="370c9-310">如果使用缓存键未找到缓存的内容，则将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="370c9-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="370c9-311">使用 `GetFileContent` 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="370c9-312">使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。</span><span class="sxs-lookup"><span data-stu-id="370c9-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="370c9-313">修改该文件时，会触发令牌的回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="370c9-314">[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="370c9-315">如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。</span><span class="sxs-lookup"><span data-stu-id="370c9-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="370c9-316">在下面的示例中，文件存储在应用的[内容根目录](xref:fundamentals/index#content-root)中。</span><span class="sxs-lookup"><span data-stu-id="370c9-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="370c9-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) 用于获取指向应用的 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="370c9-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="370c9-318">`filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。</span><span class="sxs-lookup"><span data-stu-id="370c9-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="370c9-319">将 `FileService` 与内存缓存服务一起注册在服务容器中。</span><span class="sxs-lookup"><span data-stu-id="370c9-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="370c9-320">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="370c9-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="370c9-321">页面模型使用服务加载文件内容。</span><span class="sxs-lookup"><span data-stu-id="370c9-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="370c9-322">在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs) 中：</span><span class="sxs-lookup"><span data-stu-id="370c9-322">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="370c9-323">CompositeChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="370c9-323">CompositeChangeToken class</span></span>

<span data-ttu-id="370c9-324">要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。</span><span class="sxs-lookup"><span data-stu-id="370c9-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="370c9-325">如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="370c9-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="370c9-326">如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="370c9-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="370c9-327">如果发生多个并发更改事件，则调用一次复合更改回调。</span><span class="sxs-lookup"><span data-stu-id="370c9-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="370c9-328">其他资源</span><span class="sxs-lookup"><span data-stu-id="370c9-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
