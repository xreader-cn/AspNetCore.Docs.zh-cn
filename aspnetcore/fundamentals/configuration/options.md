---
title: ASP.NET Core 中的选项模式
author: guardrex
description: 了解如何使用选项模式来表示 ASP.NET Core 应用中的相关设置组。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/18/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 4192bab8acef7c4f7bdf1ac481c468cd0a835420
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239799"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="62f5a-103">ASP.NET Core 中的选项模式</span><span class="sxs-lookup"><span data-stu-id="62f5a-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="62f5a-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="62f5a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62f5a-105">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="62f5a-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="62f5a-106">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="62f5a-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="62f5a-107">[接口分离原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="62f5a-108">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="62f5a-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="62f5a-109">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="62f5a-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="62f5a-110">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="62f5a-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="62f5a-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="62f5a-112">Package</span><span class="sxs-lookup"><span data-stu-id="62f5a-112">Package</span></span>

<span data-ttu-id="62f5a-113">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包已在 ASP.NET Core 应用中隐式引用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="62f5a-114">选项接口</span><span class="sxs-lookup"><span data-stu-id="62f5a-114">Options interfaces</span></span>

<span data-ttu-id="62f5a-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="62f5a-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="62f5a-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="62f5a-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="62f5a-117">更改通知</span><span class="sxs-lookup"><span data-stu-id="62f5a-117">Change notifications</span></span>
* [<span data-ttu-id="62f5a-118">命名选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="62f5a-119">可重载配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="62f5a-120">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="62f5a-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="62f5a-121">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="62f5a-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="62f5a-123">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="62f5a-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="62f5a-124">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="62f5a-125">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="62f5a-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="62f5a-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="62f5a-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="62f5a-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="62f5a-128">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="62f5a-129">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="62f5a-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="62f5a-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="62f5a-131">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="62f5a-132"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="62f5a-133">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="62f5a-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="62f5a-134">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="62f5a-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="62f5a-135">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-135">General options configuration</span></span>

<span data-ttu-id="62f5a-136">常规选项配置已作为示例 &num;1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="62f5a-137">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="62f5a-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="62f5a-138">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="62f5a-139">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="62f5a-140">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="62f5a-141">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="62f5a-142">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="62f5a-143">示例的 appsettings.json  文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="62f5a-144">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="62f5a-145">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="62f5a-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="62f5a-146">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="62f5a-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="62f5a-147">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="62f5a-148">通过委托配置简单选项已作为示例 &num;2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="62f5a-149">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-149">Use a delegate to set options values.</span></span> <span data-ttu-id="62f5a-150">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="62f5a-151">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="62f5a-152">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="62f5a-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="62f5a-153">Index.cshtml.cs  :</span><span class="sxs-lookup"><span data-stu-id="62f5a-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="62f5a-154">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="62f5a-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="62f5a-155">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="62f5a-156">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="62f5a-157">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="62f5a-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="62f5a-158">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json  中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="62f5a-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="62f5a-159">当启用多个配置服务时，指定的最后一个配置源优于其他源  ，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="62f5a-160">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="62f5a-161">子选项配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-161">Suboptions configuration</span></span>

<span data-ttu-id="62f5a-162">子选项配置已作为示例 &num;3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="62f5a-163">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="62f5a-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="62f5a-164">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="62f5a-165">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="62f5a-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="62f5a-166">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json  中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="62f5a-167">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="62f5a-168">它将 `MySubOptions` 绑定到 appsettings.json  文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="62f5a-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="62f5a-169">`GetSection` 方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="62f5a-169">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="62f5a-170">示例的 appsettings.json  文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="62f5a-170">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="62f5a-171">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-171">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="62f5a-172">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-172">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="62f5a-173">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-173">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="62f5a-174">选项注入</span><span class="sxs-lookup"><span data-stu-id="62f5a-174">Options injection</span></span>

<span data-ttu-id="62f5a-175">选项注入已作为示例 &num;4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-175">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="62f5a-176">将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入：</span><span class="sxs-lookup"><span data-stu-id="62f5a-176">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="62f5a-177">使用 [@inject](xref:mvc/views/razor#inject) Razor 指令的 Razor 页面或 MVC 视图。</span><span class="sxs-lookup"><span data-stu-id="62f5a-177">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="62f5a-178">页面或视图模型。</span><span class="sxs-lookup"><span data-stu-id="62f5a-178">A page or view model.</span></span>

<span data-ttu-id="62f5a-179">示例应用中的以下示例将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入页面模型 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="62f5a-179">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="62f5a-180">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="62f5a-180">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="62f5a-181">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="62f5a-181">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="62f5a-183">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="62f5a-183">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="62f5a-184">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 &num;5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-184">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="62f5a-185">通过使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>，针对请求生存期访问和缓存选项时，每个请求都会计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-185">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="62f5a-186">`IOptionsMonitor` 和 `IOptionsSnapshot` 之间的区别在于：</span><span class="sxs-lookup"><span data-stu-id="62f5a-186">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="62f5a-187">`IOptionsMonitor` 是一种[单一示例服务](xref:fundamentals/dependency-injection#singleton)，可随时检索当前选项值，这在单一实例依赖项中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-187">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="62f5a-188">`IOptionsSnapshot` 是一种[作用域服务](xref:fundamentals/dependency-injection#scoped)，并在构造 `IOptionsSnapshot<T>` 对象时提供选项的快照。</span><span class="sxs-lookup"><span data-stu-id="62f5a-188">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="62f5a-189">选项快照旨在用于暂时性和有作用域的依赖项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-189">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="62f5a-190">以下示例演示如何在更改 appsettings.json  (Pages/Index.cshtml.cs  ) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-190">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="62f5a-191">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json  文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-191">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="62f5a-192">下图显示从 appsettings.json  文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-192">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="62f5a-193">将 appsettings.json  文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-193">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="62f5a-194">保存 appsettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="62f5a-194">Save the *appsettings.json* file.</span></span> <span data-ttu-id="62f5a-195">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-195">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="62f5a-196">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="62f5a-196">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="62f5a-197">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 &num;6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-197">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="62f5a-198">命名选项  支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-198">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="62f5a-199">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用扩展方法 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)：</span><span class="sxs-lookup"><span data-stu-id="62f5a-199">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="62f5a-200">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs  ) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-200">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="62f5a-201">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-201">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="62f5a-202">从配置中提供从 appsettings.json  文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-202">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="62f5a-203">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-203">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="62f5a-204">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="62f5a-204">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="62f5a-205">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-205">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="62f5a-206">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-206">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="62f5a-207">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-207">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="62f5a-208">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-208">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="62f5a-209">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="62f5a-209">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="62f5a-210">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="62f5a-210">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="62f5a-211">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-211">All options are named instances.</span></span> <span data-ttu-id="62f5a-212">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向为 `string.Empty` 的 `Options.DefaultName` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-212">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="62f5a-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-213"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="62f5a-214"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="62f5a-214">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="62f5a-215">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="62f5a-215">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="62f5a-216">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="62f5a-216">OptionsBuilder API</span></span>

<span data-ttu-id="62f5a-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-217"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="62f5a-218">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="62f5a-218">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="62f5a-219">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="62f5a-219">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="62f5a-220">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-220">Use DI services to configure options</span></span>

<span data-ttu-id="62f5a-221">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="62f5a-221">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="62f5a-222">将配置委托传递给 [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="62f5a-222">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="62f5a-223">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-223">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="62f5a-224">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-224">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="62f5a-225">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="62f5a-225">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="62f5a-226">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="62f5a-226">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="62f5a-227">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="62f5a-227">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="62f5a-228">选项验证</span><span class="sxs-lookup"><span data-stu-id="62f5a-228">Options validation</span></span>

<span data-ttu-id="62f5a-229">借助选项验证，可以验证已配置的选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-229">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="62f5a-230">使用验证方法调用 `Validate`。如果选项有效，方法返回 `true`；如果无效，方法返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="62f5a-230">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="62f5a-231">上面的示例将命名选项实例设置为 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-231">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="62f5a-232">默认选项实例为 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-232">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="62f5a-233">选项验证在选项实例创建后运行。</span><span class="sxs-lookup"><span data-stu-id="62f5a-233">Validation runs when the options instance is created.</span></span> <span data-ttu-id="62f5a-234">系统保证在选项实例首次获得访问时通过验证。</span><span class="sxs-lookup"><span data-stu-id="62f5a-234">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="62f5a-235">选项验证无法防止在最初配置和验证选项后发生选项修改。</span><span class="sxs-lookup"><span data-stu-id="62f5a-235">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="62f5a-236">`Validate` 方法接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-236">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="62f5a-237">若要完全自定义验证，请实现 `IValidateOptions<TOptions>`，它支持：</span><span class="sxs-lookup"><span data-stu-id="62f5a-237">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="62f5a-238">验证多种类型的选项：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="62f5a-238">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="62f5a-239">验证取决于其他选项类型：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="62f5a-239">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="62f5a-240">`IValidateOptions` 验证：</span><span class="sxs-lookup"><span data-stu-id="62f5a-240">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="62f5a-241">特定的命名选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-241">A specific named options instance.</span></span>
* <span data-ttu-id="62f5a-242">所有选项（如果 `name` 为 `null` 的话）。</span><span class="sxs-lookup"><span data-stu-id="62f5a-242">All options when `name` is `null`.</span></span>

<span data-ttu-id="62f5a-243">通过接口实现返回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="62f5a-243">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="62f5a-244">通过调用 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，可以从 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 包中获得基于数据注释的验证。</span><span class="sxs-lookup"><span data-stu-id="62f5a-244">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="62f5a-245">`Microsoft.Extensions.Options.DataAnnotations` 已在 ASP.NET Core 应用中隐式引用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-245">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
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

<span data-ttu-id="62f5a-246">设计团队正在考虑为未来版本提供预先验证（在启动时快速失败）。</span><span class="sxs-lookup"><span data-stu-id="62f5a-246">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="62f5a-247">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-247">Options post-configuration</span></span>

<span data-ttu-id="62f5a-248">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-248">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="62f5a-249">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-249">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="62f5a-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-250"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="62f5a-251">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-251">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="62f5a-252">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-252">Accessing options during startup</span></span>

<span data-ttu-id="62f5a-253"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-253"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="62f5a-254">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-254">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="62f5a-255">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="62f5a-255">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="62f5a-256">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="62f5a-256">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="62f5a-257">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="62f5a-257">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="62f5a-258">[接口分离原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-258">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="62f5a-259">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="62f5a-259">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="62f5a-260">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="62f5a-260">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="62f5a-261">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-261">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="62f5a-262">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="62f5a-262">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="62f5a-263">系统必备</span><span class="sxs-lookup"><span data-stu-id="62f5a-263">Prerequisites</span></span>

<span data-ttu-id="62f5a-264">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="62f5a-264">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="62f5a-265">选项接口</span><span class="sxs-lookup"><span data-stu-id="62f5a-265">Options interfaces</span></span>

<span data-ttu-id="62f5a-266"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="62f5a-266"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="62f5a-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="62f5a-267"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="62f5a-268">更改通知</span><span class="sxs-lookup"><span data-stu-id="62f5a-268">Change notifications</span></span>
* [<span data-ttu-id="62f5a-269">命名选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-269">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="62f5a-270">可重载配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-270">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="62f5a-271">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="62f5a-271">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="62f5a-272">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-272">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="62f5a-273"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-273"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="62f5a-274">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="62f5a-274">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="62f5a-275">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-275">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="62f5a-276">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="62f5a-276">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="62f5a-277"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-277"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="62f5a-278"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="62f5a-278">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="62f5a-279">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-279">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="62f5a-280">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="62f5a-280">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="62f5a-281"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-281"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="62f5a-282">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-282">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="62f5a-283"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-283"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="62f5a-284">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="62f5a-284">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="62f5a-285">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="62f5a-285">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="62f5a-286">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-286">General options configuration</span></span>

<span data-ttu-id="62f5a-287">常规选项配置已作为示例 &num;1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-287">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="62f5a-288">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="62f5a-288">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="62f5a-289">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-289">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="62f5a-290">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-290">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="62f5a-291">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-291">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="62f5a-292">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-292">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="62f5a-293">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-293">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="62f5a-294">示例的 appsettings.json  文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-294">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="62f5a-295">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-295">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="62f5a-296">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="62f5a-296">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="62f5a-297">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="62f5a-297">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="62f5a-298">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-298">Configure simple options with a delegate</span></span>

<span data-ttu-id="62f5a-299">通过委托配置简单选项已作为示例 &num;2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-299">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="62f5a-300">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-300">Use a delegate to set options values.</span></span> <span data-ttu-id="62f5a-301">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-301">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="62f5a-302">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-302">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="62f5a-303">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="62f5a-303">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="62f5a-304">Index.cshtml.cs  :</span><span class="sxs-lookup"><span data-stu-id="62f5a-304">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="62f5a-305">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="62f5a-305">You can add multiple configuration providers.</span></span> <span data-ttu-id="62f5a-306">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-306">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="62f5a-307">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-307">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="62f5a-308">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="62f5a-308">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="62f5a-309">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json  中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="62f5a-309">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="62f5a-310">当启用多个配置服务时，指定的最后一个配置源优于其他源  ，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-310">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="62f5a-311">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-311">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="62f5a-312">子选项配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-312">Suboptions configuration</span></span>

<span data-ttu-id="62f5a-313">子选项配置已作为示例 &num;3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-313">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="62f5a-314">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="62f5a-314">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="62f5a-315">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-315">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="62f5a-316">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="62f5a-316">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="62f5a-317">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json  中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-317">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="62f5a-318">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-318">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="62f5a-319">它将 `MySubOptions` 绑定到 appsettings.json  文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="62f5a-319">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="62f5a-320">`GetSection` 方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="62f5a-320">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="62f5a-321">示例的 appsettings.json  文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="62f5a-321">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="62f5a-322">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-322">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="62f5a-323">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-323">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="62f5a-324">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-324">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="62f5a-325">选项注入</span><span class="sxs-lookup"><span data-stu-id="62f5a-325">Options injection</span></span>

<span data-ttu-id="62f5a-326">选项注入已作为示例 &num;4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-326">Options injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="62f5a-327">将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入：</span><span class="sxs-lookup"><span data-stu-id="62f5a-327">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="62f5a-328">使用 [@inject](xref:mvc/views/razor#inject) Razor 指令的 Razor 页面或 MVC 视图。</span><span class="sxs-lookup"><span data-stu-id="62f5a-328">A Razor page or MVC view with the [@inject](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="62f5a-329">页面或视图模型。</span><span class="sxs-lookup"><span data-stu-id="62f5a-329">A page or view model.</span></span>

<span data-ttu-id="62f5a-330">示例应用中的以下示例将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 注入页面模型 (*Pages/Index.cshtml.cs*)：</span><span class="sxs-lookup"><span data-stu-id="62f5a-330">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="62f5a-331">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="62f5a-331">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="62f5a-332">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="62f5a-332">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="62f5a-334">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="62f5a-334">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="62f5a-335">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 &num;5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-335">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="62f5a-336">通过使用 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>，针对请求生存期访问和缓存选项时，每个请求都会计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-336">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="62f5a-337">`IOptionsMonitor` 和 `IOptionsSnapshot` 之间的区别在于：</span><span class="sxs-lookup"><span data-stu-id="62f5a-337">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="62f5a-338">`IOptionsMonitor` 是一种[单一示例服务](xref:fundamentals/dependency-injection#singleton)，可随时检索当前选项值，这在单一实例依赖项中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-338">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="62f5a-339">`IOptionsSnapshot` 是一种[作用域服务](xref:fundamentals/dependency-injection#scoped)，并在构造 `IOptionsSnapshot<T>` 对象时提供选项的快照。</span><span class="sxs-lookup"><span data-stu-id="62f5a-339">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="62f5a-340">选项快照旨在用于暂时性和有作用域的依赖项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-340">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="62f5a-341">以下示例演示如何在更改 appsettings.json  (Pages/Index.cshtml.cs  ) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-341">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="62f5a-342">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json  文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-342">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="62f5a-343">下图显示从 appsettings.json  文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-343">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="62f5a-344">将 appsettings.json  文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-344">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="62f5a-345">保存 appsettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="62f5a-345">Save the *appsettings.json* file.</span></span> <span data-ttu-id="62f5a-346">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-346">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="62f5a-347">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="62f5a-347">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="62f5a-348">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 &num;6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-348">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="62f5a-349">命名选项  支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-349">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="62f5a-350">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用扩展方法 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)：</span><span class="sxs-lookup"><span data-stu-id="62f5a-350">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="62f5a-351">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs  ) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-351">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="62f5a-352">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-352">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="62f5a-353">从配置中提供从 appsettings.json  文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-353">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="62f5a-354">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-354">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="62f5a-355">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="62f5a-355">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="62f5a-356">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-356">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="62f5a-357">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-357">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="62f5a-358">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-358">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="62f5a-359">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-359">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="62f5a-360">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="62f5a-360">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="62f5a-361">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="62f5a-361">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="62f5a-362">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-362">All options are named instances.</span></span> <span data-ttu-id="62f5a-363">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向为 `string.Empty` 的 `Options.DefaultName` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-363">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="62f5a-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-364"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="62f5a-365"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="62f5a-365">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="62f5a-366">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="62f5a-366">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="62f5a-367">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="62f5a-367">OptionsBuilder API</span></span>

<span data-ttu-id="62f5a-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-368"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="62f5a-369">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="62f5a-369">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="62f5a-370">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="62f5a-370">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="62f5a-371">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-371">Use DI services to configure options</span></span>

<span data-ttu-id="62f5a-372">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="62f5a-372">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="62f5a-373">将配置委托传递给 [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="62f5a-373">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="62f5a-374">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-374">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="62f5a-375">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-375">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="62f5a-376">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="62f5a-376">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="62f5a-377">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="62f5a-377">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="62f5a-378">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="62f5a-378">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="62f5a-379">选项验证</span><span class="sxs-lookup"><span data-stu-id="62f5a-379">Options validation</span></span>

<span data-ttu-id="62f5a-380">借助选项验证，可以验证已配置的选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-380">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="62f5a-381">使用验证方法调用 `Validate`。如果选项有效，方法返回 `true`；如果无效，方法返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="62f5a-381">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="62f5a-382">上面的示例将命名选项实例设置为 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-382">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="62f5a-383">默认选项实例为 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-383">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="62f5a-384">选项验证在选项实例创建后运行。</span><span class="sxs-lookup"><span data-stu-id="62f5a-384">Validation runs when the options instance is created.</span></span> <span data-ttu-id="62f5a-385">系统保证在选项实例首次获得访问时通过验证。</span><span class="sxs-lookup"><span data-stu-id="62f5a-385">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="62f5a-386">选项验证无法防止在最初配置和验证选项后发生选项修改。</span><span class="sxs-lookup"><span data-stu-id="62f5a-386">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="62f5a-387">`Validate` 方法接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-387">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="62f5a-388">若要完全自定义验证，请实现 `IValidateOptions<TOptions>`，它支持：</span><span class="sxs-lookup"><span data-stu-id="62f5a-388">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="62f5a-389">验证多种类型的选项：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="62f5a-389">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="62f5a-390">验证取决于其他选项类型：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="62f5a-390">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="62f5a-391">`IValidateOptions` 验证：</span><span class="sxs-lookup"><span data-stu-id="62f5a-391">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="62f5a-392">特定的命名选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-392">A specific named options instance.</span></span>
* <span data-ttu-id="62f5a-393">所有选项（如果 `name` 为 `null` 的话）。</span><span class="sxs-lookup"><span data-stu-id="62f5a-393">All options when `name` is `null`.</span></span>

<span data-ttu-id="62f5a-394">通过接口实现返回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="62f5a-394">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="62f5a-395">通过调用 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，可以从 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 包中获得基于数据注释的验证。</span><span class="sxs-lookup"><span data-stu-id="62f5a-395">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="62f5a-396">`Microsoft.Extensions.Options.DataAnnotations` 包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="62f5a-396">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="62f5a-397">设计团队正在考虑为未来版本提供预先验证（在启动时快速失败）。</span><span class="sxs-lookup"><span data-stu-id="62f5a-397">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="62f5a-398">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-398">Options post-configuration</span></span>

<span data-ttu-id="62f5a-399">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-399">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="62f5a-400">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-400">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="62f5a-401"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-401"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="62f5a-402">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-402">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="62f5a-403">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-403">Accessing options during startup</span></span>

<span data-ttu-id="62f5a-404"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-404"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="62f5a-405">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-405">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="62f5a-406">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="62f5a-406">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="62f5a-407">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="62f5a-407">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="62f5a-408">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="62f5a-408">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="62f5a-409">[接口分离原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-409">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="62f5a-410">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="62f5a-410">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="62f5a-411">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="62f5a-411">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="62f5a-412">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-412">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="62f5a-413">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="62f5a-413">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="62f5a-414">系统必备</span><span class="sxs-lookup"><span data-stu-id="62f5a-414">Prerequisites</span></span>

<span data-ttu-id="62f5a-415">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="62f5a-415">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="62f5a-416">选项接口</span><span class="sxs-lookup"><span data-stu-id="62f5a-416">Options interfaces</span></span>

<span data-ttu-id="62f5a-417"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="62f5a-417"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="62f5a-418"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="62f5a-418"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="62f5a-419">更改通知</span><span class="sxs-lookup"><span data-stu-id="62f5a-419">Change notifications</span></span>
* [<span data-ttu-id="62f5a-420">命名选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-420">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="62f5a-421">可重载配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-421">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="62f5a-422">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="62f5a-422">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="62f5a-423">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-423">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="62f5a-424"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-424"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="62f5a-425">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="62f5a-425">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="62f5a-426">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-426">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="62f5a-427">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="62f5a-427">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="62f5a-428"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-428"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="62f5a-429"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="62f5a-429">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="62f5a-430">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-430">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="62f5a-431">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="62f5a-431">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="62f5a-432"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-432"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="62f5a-433">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-433">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="62f5a-434"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-434"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="62f5a-435">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="62f5a-435">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="62f5a-436">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="62f5a-436">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="62f5a-437">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-437">General options configuration</span></span>

<span data-ttu-id="62f5a-438">常规选项配置已作为示例 &num;1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-438">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="62f5a-439">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="62f5a-439">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="62f5a-440">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-440">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="62f5a-441">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-441">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="62f5a-442">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-442">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="62f5a-443">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-443">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="62f5a-444">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-444">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="62f5a-445">示例的 appsettings.json  文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-445">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="62f5a-446">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-446">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="62f5a-447">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="62f5a-447">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="62f5a-448">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="62f5a-448">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="62f5a-449">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-449">Configure simple options with a delegate</span></span>

<span data-ttu-id="62f5a-450">通过委托配置简单选项已作为示例 &num;2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-450">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="62f5a-451">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-451">Use a delegate to set options values.</span></span> <span data-ttu-id="62f5a-452">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-452">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="62f5a-453">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-453">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="62f5a-454">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="62f5a-454">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="62f5a-455">Index.cshtml.cs  :</span><span class="sxs-lookup"><span data-stu-id="62f5a-455">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="62f5a-456">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="62f5a-456">You can add multiple configuration providers.</span></span> <span data-ttu-id="62f5a-457">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="62f5a-457">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="62f5a-458">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-458">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="62f5a-459">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="62f5a-459">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="62f5a-460">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json  中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="62f5a-460">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="62f5a-461">当启用多个配置服务时，指定的最后一个配置源优于其他源  ，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-461">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="62f5a-462">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-462">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="62f5a-463">子选项配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-463">Suboptions configuration</span></span>

<span data-ttu-id="62f5a-464">子选项配置已作为示例 &num;3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-464">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="62f5a-465">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="62f5a-465">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="62f5a-466">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-466">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="62f5a-467">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="62f5a-467">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="62f5a-468">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json  中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-468">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="62f5a-469">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-469">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="62f5a-470">它将 `MySubOptions` 绑定到 appsettings.json  文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="62f5a-470">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="62f5a-471">`GetSection` 方法需要 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="62f5a-471">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="62f5a-472">示例的 appsettings.json  文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="62f5a-472">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="62f5a-473">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-473">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="62f5a-474">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="62f5a-474">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="62f5a-475">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="62f5a-475">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="62f5a-476">视图模型或通过直接视图注入提供的选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-476">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="62f5a-477">视图模型或通过直接视图注入提供的选项已作为示例 &num;4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-477">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="62f5a-478">可在视图模型中或通过将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接注入到视图 (Pages/Index.cshtml.cs  ) 来提供选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-478">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="62f5a-479">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="62f5a-479">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="62f5a-480">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="62f5a-480">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="62f5a-482">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="62f5a-482">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="62f5a-483">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 &num;5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-483">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="62f5a-484"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支持包含最小处理开销的重新加载选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-484"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="62f5a-485">针对请求生存期访问和缓存选项时，每个请求只能计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="62f5a-485">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="62f5a-486">以下示例演示如何在更改 appsettings.json  (Pages/Index.cshtml.cs  ) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-486">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="62f5a-487">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json  文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-487">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="62f5a-488">下图显示从 appsettings.json  文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-488">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="62f5a-489">将 appsettings.json  文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-489">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="62f5a-490">保存 appsettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="62f5a-490">Save the *appsettings.json* file.</span></span> <span data-ttu-id="62f5a-491">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-491">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="62f5a-492">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="62f5a-492">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="62f5a-493">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 &num;6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="62f5a-493">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="62f5a-494">命名选项  支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="62f5a-494">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="62f5a-495">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用扩展方法 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)：</span><span class="sxs-lookup"><span data-stu-id="62f5a-495">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="62f5a-496">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs  ) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-496">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="62f5a-497">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-497">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="62f5a-498">从配置中提供从 appsettings.json  文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-498">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="62f5a-499">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="62f5a-499">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="62f5a-500">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="62f5a-500">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="62f5a-501">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="62f5a-501">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="62f5a-502">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-502">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="62f5a-503">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-503">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="62f5a-504">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="62f5a-504">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="62f5a-505">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="62f5a-505">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="62f5a-506">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="62f5a-506">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="62f5a-507">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-507">All options are named instances.</span></span> <span data-ttu-id="62f5a-508">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向为 `string.Empty` 的 `Options.DefaultName` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-508">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="62f5a-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-509"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="62f5a-510"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="62f5a-510">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="62f5a-511">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="62f5a-511">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="62f5a-512">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="62f5a-512">OptionsBuilder API</span></span>

<span data-ttu-id="62f5a-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="62f5a-513"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="62f5a-514">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="62f5a-514">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="62f5a-515">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="62f5a-515">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="62f5a-516">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-516">Use DI services to configure options</span></span>

<span data-ttu-id="62f5a-517">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="62f5a-517">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="62f5a-518">将配置委托传递给 [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="62f5a-518">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="62f5a-519">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="62f5a-519">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="62f5a-520">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-520">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="62f5a-521">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="62f5a-521">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="62f5a-522">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="62f5a-522">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="62f5a-523">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="62f5a-523">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="62f5a-524">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="62f5a-524">Options post-configuration</span></span>

<span data-ttu-id="62f5a-525">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="62f5a-525">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="62f5a-526">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-526">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="62f5a-527"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-527"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="62f5a-528">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="62f5a-528">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="62f5a-529">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="62f5a-529">Accessing options during startup</span></span>

<span data-ttu-id="62f5a-530"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="62f5a-530"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="62f5a-531">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="62f5a-531">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="62f5a-532">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="62f5a-532">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="62f5a-533">其他资源</span><span class="sxs-lookup"><span data-stu-id="62f5a-533">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
