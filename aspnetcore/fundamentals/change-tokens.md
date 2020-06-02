---
<span data-ttu-id="58fb3-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="58fb3-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="58fb3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58fb3-102">'Blazor'</span></span>
- <span data-ttu-id="58fb3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58fb3-103">'Identity'</span></span>
- <span data-ttu-id="58fb3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58fb3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="58fb3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58fb3-105">'Razor'</span></span>
- <span data-ttu-id="58fb3-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="58fb3-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="58fb3-107">使用 ASP.NET Core 中的更改令牌检测更改</span><span class="sxs-lookup"><span data-stu-id="58fb3-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58fb3-108">更改令牌是用于跟踪状态更改的通用、低级别构建基块。</span><span class="sxs-lookup"><span data-stu-id="58fb3-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="58fb3-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="58fb3-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="58fb3-110">IChangeToken 接口</span><span class="sxs-lookup"><span data-stu-id="58fb3-110">IChangeToken interface</span></span>

<span data-ttu-id="58fb3-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="58fb3-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="58fb3-112">`IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="58fb3-113">将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="58fb3-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="58fb3-114">`IChangeToken` 具有以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="58fb3-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="58fb3-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="58fb3-116">如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。</span><span class="sxs-lookup"><span data-stu-id="58fb3-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="58fb3-117">如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="58fb3-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。</span><span class="sxs-lookup"><span data-stu-id="58fb3-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="58fb3-119">`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="58fb3-120">调用回调之前，必须设置 `HasChanged`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="58fb3-121">ChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="58fb3-121">ChangeToken class</span></span>

<span data-ttu-id="58fb3-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="58fb3-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="58fb3-123">`ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="58fb3-124">将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="58fb3-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="58fb3-125">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：</span><span class="sxs-lookup"><span data-stu-id="58fb3-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="58fb3-126">`Func<IChangeToken>` 生成令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="58fb3-127">令牌更改时，调用 `Action`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="58fb3-128">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="58fb3-129">`OnChange` 返回 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="58fb3-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="58fb3-130">调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。</span><span class="sxs-lookup"><span data-stu-id="58fb3-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="58fb3-131">ASP.NET Core 中更改令牌的使用示例</span><span class="sxs-lookup"><span data-stu-id="58fb3-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="58fb3-132">更改令牌主要用于在 ASP.NET Core 中监视对象更改：</span><span class="sxs-lookup"><span data-stu-id="58fb3-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="58fb3-133">为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="58fb3-134">可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。</span><span class="sxs-lookup"><span data-stu-id="58fb3-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="58fb3-135">对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。</span><span class="sxs-lookup"><span data-stu-id="58fb3-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="58fb3-136">每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="58fb3-137">监视配置更改</span><span class="sxs-lookup"><span data-stu-id="58fb3-137">Monitor for configuration changes</span></span>

<span data-ttu-id="58fb3-138">默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json、appsettings.Development.json 和 appsettings.Production.json）来加载应用配置设置。</span><span class="sxs-lookup"><span data-stu-id="58fb3-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="58fb3-139">使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。</span><span class="sxs-lookup"><span data-stu-id="58fb3-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="58fb3-140">`reloadOnChange` 指示文件更改时是否应该重载配置。</span><span class="sxs-lookup"><span data-stu-id="58fb3-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="58fb3-141">此设置出现在 <xref:Microsoft.Extensions.Hosting.Host> 便捷方法 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="58fb3-142">基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。</span><span class="sxs-lookup"><span data-stu-id="58fb3-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="58fb3-143">`FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。</span><span class="sxs-lookup"><span data-stu-id="58fb3-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="58fb3-144">默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="58fb3-145">示例应用演示监视配置更改的两个实现。</span><span class="sxs-lookup"><span data-stu-id="58fb3-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="58fb3-146">如果任一 appsettings 文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。</span><span class="sxs-lookup"><span data-stu-id="58fb3-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="58fb3-147">配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。</span><span class="sxs-lookup"><span data-stu-id="58fb3-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="58fb3-148">为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。</span><span class="sxs-lookup"><span data-stu-id="58fb3-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="58fb3-149">此示例使用 SHA1 文件哈希。</span><span class="sxs-lookup"><span data-stu-id="58fb3-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="58fb3-150">使用指数回退实现重试。</span><span class="sxs-lookup"><span data-stu-id="58fb3-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="58fb3-151">进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。</span><span class="sxs-lookup"><span data-stu-id="58fb3-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="58fb3-152">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="58fb3-153">简单启动更改令牌</span><span class="sxs-lookup"><span data-stu-id="58fb3-153">Simple startup change token</span></span>

<span data-ttu-id="58fb3-154">将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="58fb3-155">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="58fb3-156">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="58fb3-157">回调是 `InvokeChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="58fb3-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="58fb3-158">回调的 `state` 用于在 `IWebHostEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings 配置文件（例如开发环境中的 appsettings.Development.json）。</span><span class="sxs-lookup"><span data-stu-id="58fb3-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="58fb3-159">只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。</span><span class="sxs-lookup"><span data-stu-id="58fb3-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="58fb3-160">只要应用正在运行，该系统就会运行，并且用户不能禁用。</span><span class="sxs-lookup"><span data-stu-id="58fb3-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="58fb3-161">将配置更改作为服务进行监视</span><span class="sxs-lookup"><span data-stu-id="58fb3-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="58fb3-162">示例实现：</span><span class="sxs-lookup"><span data-stu-id="58fb3-162">The sample implements:</span></span>

* <span data-ttu-id="58fb3-163">基本启动令牌监视。</span><span class="sxs-lookup"><span data-stu-id="58fb3-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="58fb3-164">作为服务监视。</span><span class="sxs-lookup"><span data-stu-id="58fb3-164">Monitoring as a service.</span></span>
* <span data-ttu-id="58fb3-165">启用和禁用监视的机制。</span><span class="sxs-lookup"><span data-stu-id="58fb3-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="58fb3-166">示例建立 `IConfigurationMonitor` 接口。</span><span class="sxs-lookup"><span data-stu-id="58fb3-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="58fb3-167">Extensions/ConfigurationMonitor.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="58fb3-168">已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：</span><span class="sxs-lookup"><span data-stu-id="58fb3-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="58fb3-169">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="58fb3-170">`InvokeChanged` 是回调方法。</span><span class="sxs-lookup"><span data-stu-id="58fb3-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="58fb3-171">此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="58fb3-172">使用了以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="58fb3-172">Two properties are used:</span></span>

* <span data-ttu-id="58fb3-173">`MonitoringEnabled`：指示回调是否应该运行其自定义代码。</span><span class="sxs-lookup"><span data-stu-id="58fb3-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="58fb3-174">`CurrentState`：描述在 UI 中使用的当前监视状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="58fb3-175">`InvokeChanged` 方法类似于前面的方法，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="58fb3-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="58fb3-176">不运行其代码，除非 `MonitoringEnabled` 为 `true`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="58fb3-177">输出其 `WriteConsole` 输出中的当前 `state`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="58fb3-178">将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：</span><span class="sxs-lookup"><span data-stu-id="58fb3-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="58fb3-179">索引页提供对配置监视的用户控制。</span><span class="sxs-lookup"><span data-stu-id="58fb3-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="58fb3-180">将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="58fb3-181">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="58fb3-182">配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：</span><span class="sxs-lookup"><span data-stu-id="58fb3-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="58fb3-183">触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="58fb3-184">触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。</span><span class="sxs-lookup"><span data-stu-id="58fb3-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="58fb3-185">UI 启用和禁用监视的按钮。</span><span class="sxs-lookup"><span data-stu-id="58fb3-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="58fb3-186">Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="58fb3-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="58fb3-187">监视缓存文件更改</span><span class="sxs-lookup"><span data-stu-id="58fb3-187">Monitor cached file changes</span></span>

<span data-ttu-id="58fb3-188">可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="58fb3-189">[内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="58fb3-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="58fb3-190">无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧（过时）数据。</span><span class="sxs-lookup"><span data-stu-id="58fb3-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="58fb3-191">例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="58fb3-192">数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="58fb3-193">任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="58fb3-194">在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="58fb3-195">示例应用演示方法的实现。</span><span class="sxs-lookup"><span data-stu-id="58fb3-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="58fb3-196">示例使用 `GetFileContent` 来完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="58fb3-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="58fb3-197">返回文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-197">Return file content.</span></span>
* <span data-ttu-id="58fb3-198">实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。</span><span class="sxs-lookup"><span data-stu-id="58fb3-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="58fb3-199">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="58fb3-200">创建 `FileService` 以处理缓存文件查找。</span><span class="sxs-lookup"><span data-stu-id="58fb3-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="58fb3-201">服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs)。</span><span class="sxs-lookup"><span data-stu-id="58fb3-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="58fb3-202">如果使用缓存键未找到缓存的内容，则将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="58fb3-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="58fb3-203">使用 `GetFileContent` 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="58fb3-204">使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="58fb3-205">修改该文件时，会触发令牌的回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="58fb3-206">[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="58fb3-207">如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。</span><span class="sxs-lookup"><span data-stu-id="58fb3-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="58fb3-208">在下面的示例中，文件存储在应用的[内容根目录](xref:fundamentals/index#content-root)中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="58fb3-209">`IWebHostEnvironment.ContentRootFileProvider` 用于获取指向应用的 `IWebHostEnvironment.ContentRootPath` 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="58fb3-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="58fb3-210">`filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。</span><span class="sxs-lookup"><span data-stu-id="58fb3-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="58fb3-211">将 `FileService` 与内存缓存服务一起注册在服务容器中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="58fb3-212">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="58fb3-213">页面模型使用服务加载文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="58fb3-214">在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs) 中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="58fb3-215">CompositeChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="58fb3-215">CompositeChangeToken class</span></span>

<span data-ttu-id="58fb3-216">要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。</span><span class="sxs-lookup"><span data-stu-id="58fb3-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="58fb3-217">如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="58fb3-218">如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="58fb3-219">如果发生多个并发更改事件，则调用一次复合更改回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58fb3-220">更改令牌是用于跟踪状态更改的通用、低级别构建基块。</span><span class="sxs-lookup"><span data-stu-id="58fb3-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="58fb3-221">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="58fb3-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="58fb3-222">IChangeToken 接口</span><span class="sxs-lookup"><span data-stu-id="58fb3-222">IChangeToken interface</span></span>

<span data-ttu-id="58fb3-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="58fb3-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="58fb3-224">`IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="58fb3-225">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。</span><span class="sxs-lookup"><span data-stu-id="58fb3-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="58fb3-226">`IChangeToken` 具有以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="58fb3-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="58fb3-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="58fb3-228">如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。</span><span class="sxs-lookup"><span data-stu-id="58fb3-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="58fb3-229">如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="58fb3-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。</span><span class="sxs-lookup"><span data-stu-id="58fb3-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="58fb3-231">`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="58fb3-232">调用回调之前，必须设置 `HasChanged`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="58fb3-233">ChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="58fb3-233">ChangeToken class</span></span>

<span data-ttu-id="58fb3-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="58fb3-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="58fb3-235">`ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="58fb3-236">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。</span><span class="sxs-lookup"><span data-stu-id="58fb3-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="58fb3-237">[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：</span><span class="sxs-lookup"><span data-stu-id="58fb3-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="58fb3-238">`Func<IChangeToken>` 生成令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="58fb3-239">令牌更改时，调用 `Action`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="58fb3-240">[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="58fb3-241">`OnChange` 返回 <xref:System.IDisposable>。</span><span class="sxs-lookup"><span data-stu-id="58fb3-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="58fb3-242">调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。</span><span class="sxs-lookup"><span data-stu-id="58fb3-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="58fb3-243">ASP.NET Core 中更改令牌的使用示例</span><span class="sxs-lookup"><span data-stu-id="58fb3-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="58fb3-244">更改令牌主要用于在 ASP.NET Core 中监视对象更改：</span><span class="sxs-lookup"><span data-stu-id="58fb3-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="58fb3-245">为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="58fb3-246">可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。</span><span class="sxs-lookup"><span data-stu-id="58fb3-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="58fb3-247">对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。</span><span class="sxs-lookup"><span data-stu-id="58fb3-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="58fb3-248">每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="58fb3-249">监视配置更改</span><span class="sxs-lookup"><span data-stu-id="58fb3-249">Monitor for configuration changes</span></span>

<span data-ttu-id="58fb3-250">默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json、appsettings.Development.json 和 appsettings.Production.json）来加载应用配置设置。</span><span class="sxs-lookup"><span data-stu-id="58fb3-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="58fb3-251">使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。</span><span class="sxs-lookup"><span data-stu-id="58fb3-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="58fb3-252">`reloadOnChange` 指示文件更改时是否应该重载配置。</span><span class="sxs-lookup"><span data-stu-id="58fb3-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="58fb3-253">此设置出现在 <xref:Microsoft.AspNetCore.WebHost> 便捷方法 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="58fb3-254">基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。</span><span class="sxs-lookup"><span data-stu-id="58fb3-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="58fb3-255">`FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。</span><span class="sxs-lookup"><span data-stu-id="58fb3-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="58fb3-256">默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="58fb3-257">示例应用演示监视配置更改的两个实现。</span><span class="sxs-lookup"><span data-stu-id="58fb3-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="58fb3-258">如果任一 appsettings 文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。</span><span class="sxs-lookup"><span data-stu-id="58fb3-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="58fb3-259">配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。</span><span class="sxs-lookup"><span data-stu-id="58fb3-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="58fb3-260">为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。</span><span class="sxs-lookup"><span data-stu-id="58fb3-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="58fb3-261">此示例使用 SHA1 文件哈希。</span><span class="sxs-lookup"><span data-stu-id="58fb3-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="58fb3-262">使用指数回退实现重试。</span><span class="sxs-lookup"><span data-stu-id="58fb3-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="58fb3-263">进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。</span><span class="sxs-lookup"><span data-stu-id="58fb3-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="58fb3-264">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="58fb3-265">简单启动更改令牌</span><span class="sxs-lookup"><span data-stu-id="58fb3-265">Simple startup change token</span></span>

<span data-ttu-id="58fb3-266">将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="58fb3-267">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="58fb3-268">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="58fb3-269">回调是 `InvokeChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="58fb3-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="58fb3-270">回调的 `state` 用于在 `IHostingEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings 配置文件（例如开发环境中的 appsettings.Development.json）。</span><span class="sxs-lookup"><span data-stu-id="58fb3-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="58fb3-271">只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。</span><span class="sxs-lookup"><span data-stu-id="58fb3-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="58fb3-272">只要应用正在运行，该系统就会运行，并且用户不能禁用。</span><span class="sxs-lookup"><span data-stu-id="58fb3-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="58fb3-273">将配置更改作为服务进行监视</span><span class="sxs-lookup"><span data-stu-id="58fb3-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="58fb3-274">示例实现：</span><span class="sxs-lookup"><span data-stu-id="58fb3-274">The sample implements:</span></span>

* <span data-ttu-id="58fb3-275">基本启动令牌监视。</span><span class="sxs-lookup"><span data-stu-id="58fb3-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="58fb3-276">作为服务监视。</span><span class="sxs-lookup"><span data-stu-id="58fb3-276">Monitoring as a service.</span></span>
* <span data-ttu-id="58fb3-277">启用和禁用监视的机制。</span><span class="sxs-lookup"><span data-stu-id="58fb3-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="58fb3-278">示例建立 `IConfigurationMonitor` 接口。</span><span class="sxs-lookup"><span data-stu-id="58fb3-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="58fb3-279">Extensions/ConfigurationMonitor.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="58fb3-280">已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：</span><span class="sxs-lookup"><span data-stu-id="58fb3-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="58fb3-281">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="58fb3-282">`InvokeChanged` 是回调方法。</span><span class="sxs-lookup"><span data-stu-id="58fb3-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="58fb3-283">此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="58fb3-284">使用了以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="58fb3-284">Two properties are used:</span></span>

* <span data-ttu-id="58fb3-285">`MonitoringEnabled`：指示回调是否应该运行其自定义代码。</span><span class="sxs-lookup"><span data-stu-id="58fb3-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="58fb3-286">`CurrentState`：描述在 UI 中使用的当前监视状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="58fb3-287">`InvokeChanged` 方法类似于前面的方法，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="58fb3-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="58fb3-288">不运行其代码，除非 `MonitoringEnabled` 为 `true`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="58fb3-289">输出其 `WriteConsole` 输出中的当前 `state`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="58fb3-290">将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：</span><span class="sxs-lookup"><span data-stu-id="58fb3-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="58fb3-291">索引页提供对配置监视的用户控制。</span><span class="sxs-lookup"><span data-stu-id="58fb3-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="58fb3-292">将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="58fb3-293">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="58fb3-294">配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：</span><span class="sxs-lookup"><span data-stu-id="58fb3-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="58fb3-295">触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="58fb3-296">触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。</span><span class="sxs-lookup"><span data-stu-id="58fb3-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="58fb3-297">UI 启用和禁用监视的按钮。</span><span class="sxs-lookup"><span data-stu-id="58fb3-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="58fb3-298">Pages/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="58fb3-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="58fb3-299">监视缓存文件更改</span><span class="sxs-lookup"><span data-stu-id="58fb3-299">Monitor cached file changes</span></span>

<span data-ttu-id="58fb3-300">可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="58fb3-301">[内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="58fb3-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="58fb3-302">无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧（过时）数据。</span><span class="sxs-lookup"><span data-stu-id="58fb3-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="58fb3-303">例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。</span><span class="sxs-lookup"><span data-stu-id="58fb3-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="58fb3-304">数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="58fb3-305">任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="58fb3-306">在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="58fb3-307">示例应用演示方法的实现。</span><span class="sxs-lookup"><span data-stu-id="58fb3-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="58fb3-308">示例使用 `GetFileContent` 来完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="58fb3-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="58fb3-309">返回文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-309">Return file content.</span></span>
* <span data-ttu-id="58fb3-310">实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。</span><span class="sxs-lookup"><span data-stu-id="58fb3-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="58fb3-311">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="58fb3-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="58fb3-312">创建 `FileService` 以处理缓存文件查找。</span><span class="sxs-lookup"><span data-stu-id="58fb3-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="58fb3-313">服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs)。</span><span class="sxs-lookup"><span data-stu-id="58fb3-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="58fb3-314">如果使用缓存键未找到缓存的内容，则将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="58fb3-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="58fb3-315">使用 `GetFileContent` 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="58fb3-316">使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。</span><span class="sxs-lookup"><span data-stu-id="58fb3-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="58fb3-317">修改该文件时，会触发令牌的回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="58fb3-318">[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="58fb3-319">如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。</span><span class="sxs-lookup"><span data-stu-id="58fb3-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="58fb3-320">在下面的示例中，文件存储在应用的[内容根目录](xref:fundamentals/index#content-root)中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="58fb3-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) 用于获取指向应用的 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="58fb3-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="58fb3-322">`filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。</span><span class="sxs-lookup"><span data-stu-id="58fb3-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="58fb3-323">将 `FileService` 与内存缓存服务一起注册在服务容器中。</span><span class="sxs-lookup"><span data-stu-id="58fb3-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="58fb3-324">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="58fb3-325">页面模型使用服务加载文件内容。</span><span class="sxs-lookup"><span data-stu-id="58fb3-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="58fb3-326">在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs) 中：</span><span class="sxs-lookup"><span data-stu-id="58fb3-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="58fb3-327">CompositeChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="58fb3-327">CompositeChangeToken class</span></span>

<span data-ttu-id="58fb3-328">要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。</span><span class="sxs-lookup"><span data-stu-id="58fb3-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="58fb3-329">如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="58fb3-330">如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="58fb3-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="58fb3-331">如果发生多个并发更改事件，则调用一次复合更改回调。</span><span class="sxs-lookup"><span data-stu-id="58fb3-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="58fb3-332">其他资源</span><span class="sxs-lookup"><span data-stu-id="58fb3-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
