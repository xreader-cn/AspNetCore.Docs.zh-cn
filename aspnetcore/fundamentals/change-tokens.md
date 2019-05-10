---
title: 使用 ASP.NET Core 中的更改令牌检测更改
author: guardrex
description: 了解如何使用更改令牌来跟踪更改。
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886872"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="e7151-103">使用 ASP.NET Core 中的更改令牌检测更改</span><span class="sxs-lookup"><span data-stu-id="e7151-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="e7151-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e7151-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e7151-105">更改令牌是用于跟踪更改的通用、低级别构建基块。</span><span class="sxs-lookup"><span data-stu-id="e7151-105">A *change token* is a general-purpose, low-level building block used to track changes.</span></span>

<span data-ttu-id="e7151-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e7151-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="e7151-107">IChangeToken 接口</span><span class="sxs-lookup"><span data-stu-id="e7151-107">IChangeToken interface</span></span>

<span data-ttu-id="e7151-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) 传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="e7151-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propagates notifications that a change has occurred.</span></span> <span data-ttu-id="e7151-109">`IChangeToken` 位于 [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="e7151-109">`IChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="e7151-110">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 及更高版本）的应用，将在项目文件中引用 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="e7151-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="e7151-111">`IChangeToken` 具有以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="e7151-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="e7151-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks)，指示令牌是否主动引发回调。</span><span class="sxs-lookup"><span data-stu-id="e7151-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="e7151-113">如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。</span><span class="sxs-lookup"><span data-stu-id="e7151-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="e7151-114">如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。</span><span class="sxs-lookup"><span data-stu-id="e7151-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="e7151-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged)，获取一个指示是否发生更改的值。</span><span class="sxs-lookup"><span data-stu-id="e7151-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) gets a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="e7151-116">此接口具有一种方法，即 [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback)，用于注册在令牌更改时调用的回调。</span><span class="sxs-lookup"><span data-stu-id="e7151-116">The interface has one method, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="e7151-117">调用回调之前，必须设置 `HasChanged`。</span><span class="sxs-lookup"><span data-stu-id="e7151-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="e7151-118">ChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="e7151-118">ChangeToken class</span></span>

<span data-ttu-id="e7151-119">`ChangeToken` 是静态类，用于传播已发生更改的通知。</span><span class="sxs-lookup"><span data-stu-id="e7151-119">`ChangeToken` is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="e7151-120">`ChangeToken` 位于 [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="e7151-120">`ChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="e7151-121">对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将在项目文件中引用 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="e7151-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="e7151-122">`ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) 方法注册令牌更改时要调用的 `Action`：</span><span class="sxs-lookup"><span data-stu-id="e7151-122">The `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="e7151-123">`Func<IChangeToken>` 生成令牌。</span><span class="sxs-lookup"><span data-stu-id="e7151-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="e7151-124">令牌更改时，调用 `Action`。</span><span class="sxs-lookup"><span data-stu-id="e7151-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="e7151-125">`ChangeToken` 具有 [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) 重载，它接受传递到令牌使用者 `Action` 的附加 `TState` 参数。</span><span class="sxs-lookup"><span data-stu-id="e7151-125">`ChangeToken` has an [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) overload that takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="e7151-126">`OnChange` 返回 [IDisposable](/dotnet/api/system.idisposable)。</span><span class="sxs-lookup"><span data-stu-id="e7151-126">`OnChange` returns an [IDisposable](/dotnet/api/system.idisposable).</span></span> <span data-ttu-id="e7151-127">调用 [Dispose](/dotnet/api/system.idisposable.dispose) 将使令牌停止侦听更多更改并释放令牌的资源。</span><span class="sxs-lookup"><span data-stu-id="e7151-127">Calling [Dispose](/dotnet/api/system.idisposable.dispose) stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="e7151-128">ASP.NET Core 中更改令牌的使用示例</span><span class="sxs-lookup"><span data-stu-id="e7151-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="e7151-129">更改令牌主要用于 ASP.NET Core 监视对象更改：</span><span class="sxs-lookup"><span data-stu-id="e7151-129">Change tokens are used in prominent areas of ASP.NET Core monitoring changes to objects:</span></span>

* <span data-ttu-id="e7151-130">为了监视文件更改，[IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) 的 [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。</span><span class="sxs-lookup"><span data-stu-id="e7151-130">For monitoring changes to files, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)'s [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="e7151-131">可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。</span><span class="sxs-lookup"><span data-stu-id="e7151-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="e7151-132">对于 `TOptions` 更改，[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) 的默认 [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) 实现具有重载，该重载接受一个或多个 [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1) 实例。</span><span class="sxs-lookup"><span data-stu-id="e7151-132">For `TOptions` changes, the default [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) implementation of [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) has an overload that accepts one or more [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1) instances.</span></span> <span data-ttu-id="e7151-133">每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。</span><span class="sxs-lookup"><span data-stu-id="e7151-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitoring-for-configuration-changes"></a><span data-ttu-id="e7151-134">监视配置更改</span><span class="sxs-lookup"><span data-stu-id="e7151-134">Monitoring for configuration changes</span></span>

<span data-ttu-id="e7151-135">默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json、appsettings.Development.json 和 appsettings.Production.json）来加载应用配置设置。</span><span class="sxs-lookup"><span data-stu-id="e7151-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="e7151-136">使用接受 `reloadOnChange` 参数（ASP.NET Core 1.1 以及更高版本）的 [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) 扩展方法配置这些文件。</span><span class="sxs-lookup"><span data-stu-id="e7151-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) extension method on [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) that accepts a `reloadOnChange` parameter (ASP.NET Core 1.1 and later).</span></span> <span data-ttu-id="e7151-137">`reloadOnChange` 指示文件更改时是否应该重载配置。</span><span class="sxs-lookup"><span data-stu-id="e7151-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="e7151-138">请参阅 [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) 便捷方法 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 中的此设置：</span><span class="sxs-lookup"><span data-stu-id="e7151-138">See this setting in the [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) convenience method [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

<span data-ttu-id="e7151-139">基于文件的配置由 [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource) 表示。</span><span class="sxs-lookup"><span data-stu-id="e7151-139">File-based configuration is represented by [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span></span> <span data-ttu-id="e7151-140">`FileConfigurationSource` 使用 [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) 监视文件。</span><span class="sxs-lookup"><span data-stu-id="e7151-140">`FileConfigurationSource` uses [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) to monitor files.</span></span>

<span data-ttu-id="e7151-141">默认情况下，由使用 [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) 来监视配置文件更改的 [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) 提供 `IFileMonitor`。</span><span class="sxs-lookup"><span data-stu-id="e7151-141">By default, the `IFileMonitor` is provided by a [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), which uses [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) to monitor for configuration file changes.</span></span>

<span data-ttu-id="e7151-142">示例应用演示监视配置更改的两个实现。</span><span class="sxs-lookup"><span data-stu-id="e7151-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="e7151-143">如果 appsettings.json 文件更改或者文件的 Environment 版本更改，则每个实现执行自定义代码。</span><span class="sxs-lookup"><span data-stu-id="e7151-143">If either the *appsettings.json* file changes or the Environment version of the file changes, each implementation executes custom code.</span></span> <span data-ttu-id="e7151-144">示例应用向控制台写入消息。</span><span class="sxs-lookup"><span data-stu-id="e7151-144">The sample app writes a message to the console.</span></span>

<span data-ttu-id="e7151-145">配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。</span><span class="sxs-lookup"><span data-stu-id="e7151-145">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="e7151-146">该示例的实现通过检查配置文件上的文件哈希来防范此问题。</span><span class="sxs-lookup"><span data-stu-id="e7151-146">The sample's implementation guards against this problem by checking file hashes on the configuration files.</span></span> <span data-ttu-id="e7151-147">检查文件哈希可确保在运行自定义代码之前，至少已更改了其中一个配置文件。</span><span class="sxs-lookup"><span data-stu-id="e7151-147">Checking file hashes ensures that at least one of the configuration files has changed before running the custom code.</span></span> <span data-ttu-id="e7151-148">本示例使用 SHA1 文件哈希 (Utilities/Utilities.cs)：</span><span class="sxs-lookup"><span data-stu-id="e7151-148">The sample uses SHA1 file hashing (*Utilities/Utilities.cs*):</span></span>

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   <span data-ttu-id="e7151-149">使用指数回退实现重试。</span><span class="sxs-lookup"><span data-stu-id="e7151-149">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="e7151-150">进行重试是因为文件锁定可能暂时阻止对其中一个文件计算新的哈希。</span><span class="sxs-lookup"><span data-stu-id="e7151-150">The re-try is present because file locking may occur that temporarily prevents computing a new hash on one of the files.</span></span>

### <a name="simple-startup-change-token"></a><span data-ttu-id="e7151-151">简单启动更改令牌</span><span class="sxs-lookup"><span data-stu-id="e7151-151">Simple startup change token</span></span>

<span data-ttu-id="e7151-152">将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌 (Startup.cs)：</span><span class="sxs-lookup"><span data-stu-id="e7151-152">Register a token consumer `Action` callback for change notifications to the configuration reload token (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="e7151-153">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="e7151-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="e7151-154">回调是 `InvokeChanged` 方法：</span><span class="sxs-lookup"><span data-stu-id="e7151-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

<span data-ttu-id="e7151-155">回调的 `state` 用于在 `IHostingEnvironment` 中传递。</span><span class="sxs-lookup"><span data-stu-id="e7151-155">The `state` of the callback is used to pass in the `IHostingEnvironment`.</span></span> <span data-ttu-id="e7151-156">这有助于确定要监视的正确 appsettings 配置 JSON 文件，即 appsettings.&lt;Environment&gt;.json。</span><span class="sxs-lookup"><span data-stu-id="e7151-156">This is useful to determine the correct *appsettings* configuration JSON file to monitor, *appsettings.&lt;Environment&gt;.json*.</span></span> <span data-ttu-id="e7151-157">只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。</span><span class="sxs-lookup"><span data-stu-id="e7151-157">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="e7151-158">只要应用正在运行，该系统就会运行，并且用户不能禁用。</span><span class="sxs-lookup"><span data-stu-id="e7151-158">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitoring-configuration-changes-as-a-service"></a><span data-ttu-id="e7151-159">将配置更改作为服务进行监视</span><span class="sxs-lookup"><span data-stu-id="e7151-159">Monitoring configuration changes as a service</span></span>

<span data-ttu-id="e7151-160">示例实现：</span><span class="sxs-lookup"><span data-stu-id="e7151-160">The sample implements:</span></span>

* <span data-ttu-id="e7151-161">基本启动令牌监视。</span><span class="sxs-lookup"><span data-stu-id="e7151-161">Basic startup token monitoring.</span></span>
* <span data-ttu-id="e7151-162">作为服务监视。</span><span class="sxs-lookup"><span data-stu-id="e7151-162">Monitoring as a service.</span></span>
* <span data-ttu-id="e7151-163">启用和禁用监视的机制。</span><span class="sxs-lookup"><span data-stu-id="e7151-163">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="e7151-164">示例建立 `IConfigurationMonitor` 接口 (Extensions/ConfigurationMonitor.cs)：</span><span class="sxs-lookup"><span data-stu-id="e7151-164">The sample establishes an `IConfigurationMonitor` interface (*Extensions/ConfigurationMonitor.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="e7151-165">已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：</span><span class="sxs-lookup"><span data-stu-id="e7151-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="e7151-166">`config.GetReloadToken()` 提供令牌。</span><span class="sxs-lookup"><span data-stu-id="e7151-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="e7151-167">`InvokeChanged` 是回调方法。</span><span class="sxs-lookup"><span data-stu-id="e7151-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="e7151-168">此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。</span><span class="sxs-lookup"><span data-stu-id="e7151-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that is used to access the monitoring state.</span></span> <span data-ttu-id="e7151-169">使用了以下两个属性：</span><span class="sxs-lookup"><span data-stu-id="e7151-169">Two properties are used:</span></span>

* <span data-ttu-id="e7151-170">`MonitoringEnabled`，指示回调是否应该运行其自定义代码。</span><span class="sxs-lookup"><span data-stu-id="e7151-170">`MonitoringEnabled` indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="e7151-171">`CurrentState`，描述在 UI 中使用的当前监视状态。</span><span class="sxs-lookup"><span data-stu-id="e7151-171">`CurrentState` describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="e7151-172">`InvokeChanged` 方法类似于前面的方法，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="e7151-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="e7151-173">不运行其代码，除非 `MonitoringEnabled` 为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e7151-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="e7151-174">注意其 `WriteConsole` 输出中的当前 `state`。</span><span class="sxs-lookup"><span data-stu-id="e7151-174">Notes the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="e7151-175">将实例 `ConfigurationMonitor` 作为服务注册在 Startup.cs 的 `ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="e7151-175">An instance `ConfigurationMonitor` is registered as a service in `ConfigureServices` of *Startup.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="e7151-176">索引页提供对配置监视的用户控制。</span><span class="sxs-lookup"><span data-stu-id="e7151-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="e7151-177">将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中：</span><span class="sxs-lookup"><span data-stu-id="e7151-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`:</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="e7151-178">按钮启用和禁用监视：</span><span class="sxs-lookup"><span data-stu-id="e7151-178">A button enables and disables monitoring:</span></span>

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="e7151-179">触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。</span><span class="sxs-lookup"><span data-stu-id="e7151-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="e7151-180">触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。</span><span class="sxs-lookup"><span data-stu-id="e7151-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

## <a name="monitoring-cached-file-changes"></a><span data-ttu-id="e7151-181">监视缓存文件更改</span><span class="sxs-lookup"><span data-stu-id="e7151-181">Monitoring cached file changes</span></span>

<span data-ttu-id="e7151-182">可以使用 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) 将文件内容缓存在内存中。</span><span class="sxs-lookup"><span data-stu-id="e7151-182">File content can be cached in-memory using [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="e7151-183">[内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="e7151-183">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="e7151-184">无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧（过时）数据。</span><span class="sxs-lookup"><span data-stu-id="e7151-184">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="e7151-185">当续订[可调过期](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration)时段造成陈旧缓存数据时，不考虑所缓存源文件的状态。</span><span class="sxs-lookup"><span data-stu-id="e7151-185">Not taking into account the status of a cached source file when renewing a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period leads to stale cache data.</span></span> <span data-ttu-id="e7151-186">数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。</span><span class="sxs-lookup"><span data-stu-id="e7151-186">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="e7151-187">任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。</span><span class="sxs-lookup"><span data-stu-id="e7151-187">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="e7151-188">在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。</span><span class="sxs-lookup"><span data-stu-id="e7151-188">Using change tokens in a file caching scenario prevents stale file content in the cache.</span></span> <span data-ttu-id="e7151-189">示例应用演示方法的实现。</span><span class="sxs-lookup"><span data-stu-id="e7151-189">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="e7151-190">示例使用 `GetFileContent` 来完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="e7151-190">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="e7151-191">返回文件内容。</span><span class="sxs-lookup"><span data-stu-id="e7151-191">Return file content.</span></span>
* <span data-ttu-id="e7151-192">实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。</span><span class="sxs-lookup"><span data-stu-id="e7151-192">Implement a retry algorithm with exponential back-off to cover cases where a file lock is temporarily preventing a file from being read.</span></span>

<span data-ttu-id="e7151-193">Utilities/Utilities.cs：</span><span class="sxs-lookup"><span data-stu-id="e7151-193">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="e7151-194">创建 `FileService` 以处理缓存文件查找。</span><span class="sxs-lookup"><span data-stu-id="e7151-194">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="e7151-195">服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs)。</span><span class="sxs-lookup"><span data-stu-id="e7151-195">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="e7151-196">如果使用缓存键未找到缓存的内容，则将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e7151-196">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="e7151-197">使用 `GetFileContent` 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="e7151-197">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="e7151-198">使用 [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) 从文件提供程序获取更改令牌。</span><span class="sxs-lookup"><span data-stu-id="e7151-198">A change token is obtained from the file provider with [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span></span> <span data-ttu-id="e7151-199">修改该文件时，会触发令牌的回调。</span><span class="sxs-lookup"><span data-stu-id="e7151-199">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="e7151-200">[可调过期](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration)时段将缓存文件内容。</span><span class="sxs-lookup"><span data-stu-id="e7151-200">The file content is cached with a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period.</span></span> <span data-ttu-id="e7151-201">如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) 连接在一起，以逐出缓存条目。</span><span class="sxs-lookup"><span data-stu-id="e7151-201">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) to evict the cache entry if the file changes while it's cached.</span></span>

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="e7151-202">将 `FileService` 和内存缓存服务 (Startup.cs) 一起注册在服务容器中：</span><span class="sxs-lookup"><span data-stu-id="e7151-202">The `FileService` is registered in the service container along with the memory caching service (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

<span data-ttu-id="e7151-203">页面模型使用服务 (Pages/Index.cshtml.cs) 加载文件内容：</span><span class="sxs-lookup"><span data-stu-id="e7151-203">The page model loads the file's content using the service (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="e7151-204">CompositeChangeToken 类</span><span class="sxs-lookup"><span data-stu-id="e7151-204">CompositeChangeToken class</span></span>

<span data-ttu-id="e7151-205">要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) 类。</span><span class="sxs-lookup"><span data-stu-id="e7151-205">For representing one or more `IChangeToken` instances in a single object, use the [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) class.</span></span>

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

<span data-ttu-id="e7151-206">如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="e7151-206">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="e7151-207">如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。</span><span class="sxs-lookup"><span data-stu-id="e7151-207">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="e7151-208">如果发生多个并发更改事件，则调用一次复合更改回调。</span><span class="sxs-lookup"><span data-stu-id="e7151-208">If multiple concurrent change events occur, the composite change callback is invoked exactly one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7151-209">其他资源</span><span class="sxs-lookup"><span data-stu-id="e7151-209">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
