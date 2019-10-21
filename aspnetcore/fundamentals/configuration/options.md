---
title: ASP.NET Core 中的选项模式
author: guardrex
description: 了解如何使用选项模式来表示 ASP.NET Core 应用中的相关设置组。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: eb0b7f3f4596b63cf3142017c5c5fe4923aac3a4
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378746"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="ed3b2-103">ASP.NET Core 中的选项模式</span><span class="sxs-lookup"><span data-stu-id="ed3b2-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="ed3b2-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ed3b2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ed3b2-105">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="ed3b2-106">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="ed3b2-107">[接口分离原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="ed3b2-108">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="ed3b2-109">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="ed3b2-110">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="ed3b2-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ed3b2-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="ed3b2-112">Package</span><span class="sxs-lookup"><span data-stu-id="ed3b2-112">Package</span></span>

<span data-ttu-id="ed3b2-113">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包已在 ASP.NET Core 应用中隐式引用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="ed3b2-114">选项接口</span><span class="sxs-lookup"><span data-stu-id="ed3b2-114">Options interfaces</span></span>

<span data-ttu-id="ed3b2-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="ed3b2-117">更改通知</span><span class="sxs-lookup"><span data-stu-id="ed3b2-117">Change notifications</span></span>
* [<span data-ttu-id="ed3b2-118">命名选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="ed3b2-119">可重载配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="ed3b2-120">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="ed3b2-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="ed3b2-121">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="ed3b2-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="ed3b2-123">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="ed3b2-124">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="ed3b2-125">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="ed3b2-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="ed3b2-128">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="ed3b2-129">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="ed3b2-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="ed3b2-131">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="ed3b2-132"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="ed3b2-133">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="ed3b2-134">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="ed3b2-135">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-135">General options configuration</span></span>

<span data-ttu-id="ed3b2-136">常规选项配置已作为示例 &num;1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="ed3b2-137">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="ed3b2-138">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="ed3b2-139">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="ed3b2-140">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="ed3b2-141">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="ed3b2-142">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="ed3b2-143">示例的 appsettings.json  文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="ed3b2-144">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="ed3b2-145">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="ed3b2-146">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="ed3b2-147">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="ed3b2-148">通过委托配置简单选项已作为示例 &num;2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="ed3b2-149">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-149">Use a delegate to set options values.</span></span> <span data-ttu-id="ed3b2-150">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="ed3b2-151">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ed3b2-152">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="ed3b2-153">Index.cshtml.cs  :</span><span class="sxs-lookup"><span data-stu-id="ed3b2-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="ed3b2-154">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="ed3b2-155">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="ed3b2-156">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ed3b2-157">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="ed3b2-158">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json  中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="ed3b2-159">当启用多个配置服务时，指定的最后一个配置源优于其他源  ，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="ed3b2-160">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="ed3b2-161">子选项配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-161">Suboptions configuration</span></span>

<span data-ttu-id="ed3b2-162">子选项配置已作为示例 &num;3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="ed3b2-163">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="ed3b2-164">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="ed3b2-165">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="ed3b2-166">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json  中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="ed3b2-167">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ed3b2-168">它将 `MySubOptions` 绑定到 appsettings.json  文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="ed3b2-169">`GetSection` 扩展方法需要 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-169">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="ed3b2-170">`Microsoft.Extensions.Options.ConfigurationExtensions` 已在 ASP.NET Core 应用中隐式引用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-170">`Microsoft.Extensions.Options.ConfigurationExtensions` is implicitly referenced in ASP.NET Core apps.</span></span>

<span data-ttu-id="ed3b2-171">示例的 appsettings.json  文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-171">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="ed3b2-172">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-172">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="ed3b2-173">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-173">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="ed3b2-174">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-174">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="ed3b2-175">视图模型或通过直接视图注入提供的选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-175">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="ed3b2-176">视图模型或通过直接视图注入提供的选项已作为示例 &num;4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-176">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="ed3b2-177">可在视图模型中或通过将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接注入到视图 (Pages/Index.cshtml.cs  ) 来提供选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-177">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="ed3b2-178">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-178">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="ed3b2-179">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-179">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="ed3b2-181">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="ed3b2-181">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="ed3b2-182">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 &num;5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-182">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="ed3b2-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支持包含最小处理开销的重新加载选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="ed3b2-184">针对请求生存期访问和缓存选项时，每个请求只能计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-184">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="ed3b2-185">以下示例演示如何在更改 appsettings.json  (Pages/Index.cshtml.cs  ) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-185">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="ed3b2-186">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json  文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-186">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="ed3b2-187">下图显示从 appsettings.json  文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-187">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="ed3b2-188">将 appsettings.json  文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-188">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="ed3b2-189">保存 appsettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-189">Save the *appsettings.json* file.</span></span> <span data-ttu-id="ed3b2-190">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-190">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="ed3b2-191">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="ed3b2-191">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="ed3b2-192">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 &num;6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-192">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="ed3b2-193">命名选项  支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-193">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="ed3b2-194">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用扩展方法 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-194">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="ed3b2-195">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs  ) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-195">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="ed3b2-196">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-196">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="ed3b2-197">从配置中提供从 appsettings.json  文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-197">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="ed3b2-198">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-198">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="ed3b2-199">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-199">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="ed3b2-200">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-200">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="ed3b2-201">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-201">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="ed3b2-202">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-202">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="ed3b2-203">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-203">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="ed3b2-204">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-204">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="ed3b2-205">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-205">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="ed3b2-206">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-206">All options are named instances.</span></span> <span data-ttu-id="ed3b2-207">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向为 `string.Empty` 的 `Options.DefaultName` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-207">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="ed3b2-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="ed3b2-209"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-209">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="ed3b2-210">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-210">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="ed3b2-211">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="ed3b2-211">OptionsBuilder API</span></span>

<span data-ttu-id="ed3b2-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-213">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-213">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="ed3b2-214">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-214">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="ed3b2-215">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-215">Use DI services to configure options</span></span>

<span data-ttu-id="ed3b2-216">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-216">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="ed3b2-217">将配置委托传递给 [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-217">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="ed3b2-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="ed3b2-219">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-219">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="ed3b2-220">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-220">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="ed3b2-221">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-221">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="ed3b2-222">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-222">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="ed3b2-223">选项验证</span><span class="sxs-lookup"><span data-stu-id="ed3b2-223">Options validation</span></span>

<span data-ttu-id="ed3b2-224">借助选项验证，可以验证已配置的选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-224">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="ed3b2-225">使用验证方法调用 `Validate`。如果选项有效，方法返回 `true`；如果无效，方法返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-225">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="ed3b2-226">上面的示例将命名选项实例设置为 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-226">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="ed3b2-227">默认选项实例为 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-227">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="ed3b2-228">选项验证在选项实例创建后运行。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-228">Validation runs when the options instance is created.</span></span> <span data-ttu-id="ed3b2-229">系统保证在选项实例首次获得访问时通过验证。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-229">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ed3b2-230">选项验证无法防止在最初配置和验证选项后发生选项修改。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-230">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="ed3b2-231">`Validate` 方法接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-231">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="ed3b2-232">若要完全自定义验证，请实现 `IValidateOptions<TOptions>`，它支持：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-232">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="ed3b2-233">验证多种类型的选项：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="ed3b2-233">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="ed3b2-234">验证取决于其他选项类型：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="ed3b2-234">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="ed3b2-235">`IValidateOptions` 验证：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-235">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="ed3b2-236">特定的命名选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-236">A specific named options instance.</span></span>
* <span data-ttu-id="ed3b2-237">所有选项（如果 `name` 为 `null` 的话）。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-237">All options when `name` is `null`.</span></span>

<span data-ttu-id="ed3b2-238">通过接口实现返回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-238">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="ed3b2-239">通过调用 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，可以从 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 包中获得基于数据注释的验证。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-239">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="ed3b2-240">`Microsoft.Extensions.Options.DataAnnotations` 已在 ASP.NET Core 应用中隐式引用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-240">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

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

<span data-ttu-id="ed3b2-241">设计团队正在考虑为未来版本提供预先验证（在启动时快速失败）。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-241">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="ed3b2-242">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-242">Options post-configuration</span></span>

<span data-ttu-id="ed3b2-243">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-243">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="ed3b2-244">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-244">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ed3b2-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ed3b2-246">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="ed3b2-247">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-247">Accessing options during startup</span></span>

<span data-ttu-id="ed3b2-248"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-248"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="ed3b2-249">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-249">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ed3b2-250">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-250">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ed3b2-251">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-251">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="ed3b2-252">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-252">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="ed3b2-253">[接口分离原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-253">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="ed3b2-254">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-254">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="ed3b2-255">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-255">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="ed3b2-256">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-256">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="ed3b2-257">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ed3b2-257">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ed3b2-258">系统必备</span><span class="sxs-lookup"><span data-stu-id="ed3b2-258">Prerequisites</span></span>

<span data-ttu-id="ed3b2-259">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-259">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="ed3b2-260">选项接口</span><span class="sxs-lookup"><span data-stu-id="ed3b2-260">Options interfaces</span></span>

<span data-ttu-id="ed3b2-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="ed3b2-263">更改通知</span><span class="sxs-lookup"><span data-stu-id="ed3b2-263">Change notifications</span></span>
* [<span data-ttu-id="ed3b2-264">命名选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-264">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="ed3b2-265">可重载配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-265">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="ed3b2-266">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="ed3b2-266">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="ed3b2-267">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-267">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="ed3b2-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="ed3b2-269">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-269">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="ed3b2-270">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-270">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="ed3b2-271">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-271">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="ed3b2-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-273"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-273">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="ed3b2-274">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-274">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="ed3b2-275">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-275">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="ed3b2-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="ed3b2-277">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-277">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="ed3b2-278"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-278"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="ed3b2-279">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-279">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="ed3b2-280">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-280">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="ed3b2-281">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-281">General options configuration</span></span>

<span data-ttu-id="ed3b2-282">常规选项配置已作为示例 &num;1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-282">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="ed3b2-283">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-283">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="ed3b2-284">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-284">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="ed3b2-285">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-285">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="ed3b2-286">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-286">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="ed3b2-287">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-287">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="ed3b2-288">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-288">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="ed3b2-289">示例的 appsettings.json  文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-289">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="ed3b2-290">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-290">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="ed3b2-291">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-291">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="ed3b2-292">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-292">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="ed3b2-293">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-293">Configure simple options with a delegate</span></span>

<span data-ttu-id="ed3b2-294">通过委托配置简单选项已作为示例 &num;2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-294">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="ed3b2-295">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-295">Use a delegate to set options values.</span></span> <span data-ttu-id="ed3b2-296">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-296">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="ed3b2-297">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-297">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ed3b2-298">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-298">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="ed3b2-299">Index.cshtml.cs  :</span><span class="sxs-lookup"><span data-stu-id="ed3b2-299">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="ed3b2-300">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-300">You can add multiple configuration providers.</span></span> <span data-ttu-id="ed3b2-301">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-301">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="ed3b2-302">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-302">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ed3b2-303">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-303">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="ed3b2-304">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json  中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-304">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="ed3b2-305">当启用多个配置服务时，指定的最后一个配置源优于其他源  ，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-305">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="ed3b2-306">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-306">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="ed3b2-307">子选项配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-307">Suboptions configuration</span></span>

<span data-ttu-id="ed3b2-308">子选项配置已作为示例 &num;3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-308">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="ed3b2-309">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-309">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="ed3b2-310">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-310">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="ed3b2-311">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-311">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="ed3b2-312">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json  中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-312">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="ed3b2-313">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-313">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ed3b2-314">它将 `MySubOptions` 绑定到 appsettings.json  文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-314">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="ed3b2-315">`GetSection` 扩展方法需要 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-315">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="ed3b2-316">如果应用使用 [Microsoft.AspNetCore.App metapackage 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本），将自动包含此包。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-316">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="ed3b2-317">示例的 appsettings.json  文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-317">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="ed3b2-318">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-318">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="ed3b2-319">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-319">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="ed3b2-320">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-320">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="ed3b2-321">视图模型或通过直接视图注入提供的选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-321">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="ed3b2-322">视图模型或通过直接视图注入提供的选项已作为示例 &num;4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-322">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="ed3b2-323">可在视图模型中或通过将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接注入到视图 (Pages/Index.cshtml.cs  ) 来提供选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-323">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="ed3b2-324">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-324">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="ed3b2-325">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-325">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="ed3b2-327">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="ed3b2-327">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="ed3b2-328">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 &num;5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-328">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="ed3b2-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支持包含最小处理开销的重新加载选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="ed3b2-330">针对请求生存期访问和缓存选项时，每个请求只能计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-330">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="ed3b2-331">以下示例演示如何在更改 appsettings.json  (Pages/Index.cshtml.cs  ) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-331">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="ed3b2-332">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json  文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-332">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="ed3b2-333">下图显示从 appsettings.json  文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-333">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="ed3b2-334">将 appsettings.json  文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-334">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="ed3b2-335">保存 appsettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-335">Save the *appsettings.json* file.</span></span> <span data-ttu-id="ed3b2-336">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-336">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="ed3b2-337">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="ed3b2-337">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="ed3b2-338">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 &num;6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-338">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="ed3b2-339">命名选项  支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-339">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="ed3b2-340">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用扩展方法 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-340">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="ed3b2-341">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs  ) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-341">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="ed3b2-342">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-342">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="ed3b2-343">从配置中提供从 appsettings.json  文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-343">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="ed3b2-344">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-344">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="ed3b2-345">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-345">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="ed3b2-346">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-346">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="ed3b2-347">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-347">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="ed3b2-348">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-348">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="ed3b2-349">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-349">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="ed3b2-350">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-350">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="ed3b2-351">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-351">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="ed3b2-352">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-352">All options are named instances.</span></span> <span data-ttu-id="ed3b2-353">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向为 `string.Empty` 的 `Options.DefaultName` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-353">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="ed3b2-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="ed3b2-355"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-355">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="ed3b2-356">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-356">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="ed3b2-357">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="ed3b2-357">OptionsBuilder API</span></span>

<span data-ttu-id="ed3b2-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-359">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-359">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="ed3b2-360">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-360">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="ed3b2-361">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-361">Use DI services to configure options</span></span>

<span data-ttu-id="ed3b2-362">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-362">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="ed3b2-363">将配置委托传递给 [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-363">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="ed3b2-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="ed3b2-365">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-365">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="ed3b2-366">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-366">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="ed3b2-367">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-367">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="ed3b2-368">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-368">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="ed3b2-369">选项验证</span><span class="sxs-lookup"><span data-stu-id="ed3b2-369">Options validation</span></span>

<span data-ttu-id="ed3b2-370">借助选项验证，可以验证已配置的选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-370">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="ed3b2-371">使用验证方法调用 `Validate`。如果选项有效，方法返回 `true`；如果无效，方法返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-371">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="ed3b2-372">上面的示例将命名选项实例设置为 `optionalOptionsName`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-372">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="ed3b2-373">默认选项实例为 `Options.DefaultName`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-373">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="ed3b2-374">选项验证在选项实例创建后运行。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-374">Validation runs when the options instance is created.</span></span> <span data-ttu-id="ed3b2-375">系统保证在选项实例首次获得访问时通过验证。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-375">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ed3b2-376">选项验证无法防止在最初配置和验证选项后发生选项修改。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-376">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="ed3b2-377">`Validate` 方法接受 `Func<TOptions, bool>`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-377">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="ed3b2-378">若要完全自定义验证，请实现 `IValidateOptions<TOptions>`，它支持：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-378">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="ed3b2-379">验证多种类型的选项：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="ed3b2-379">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="ed3b2-380">验证取决于其他选项类型：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="ed3b2-380">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="ed3b2-381">`IValidateOptions` 验证：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-381">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="ed3b2-382">特定的命名选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-382">A specific named options instance.</span></span>
* <span data-ttu-id="ed3b2-383">所有选项（如果 `name` 为 `null` 的话）。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-383">All options when `name` is `null`.</span></span>

<span data-ttu-id="ed3b2-384">通过接口实现返回 `ValidateOptionsResult`：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-384">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="ed3b2-385">通过调用 `OptionsBuilder<TOptions>` 上的 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 方法，可以从 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 包中获得基于数据注释的验证。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-385">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="ed3b2-386">`Microsoft.Extensions.Options.DataAnnotations` 包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-386">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="ed3b2-387">设计团队正在考虑为未来版本提供预先验证（在启动时快速失败）。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-387">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="ed3b2-388">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-388">Options post-configuration</span></span>

<span data-ttu-id="ed3b2-389">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-389">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="ed3b2-390">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-390">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ed3b2-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ed3b2-392">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="ed3b2-393">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-393">Accessing options during startup</span></span>

<span data-ttu-id="ed3b2-394"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-394"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="ed3b2-395">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-395">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ed3b2-396">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-396">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ed3b2-397">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-397">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="ed3b2-398">当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-398">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="ed3b2-399">[接口分离原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 依赖于配置设置的方案（类）仅依赖于其使用的配置设置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-399">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="ed3b2-400">[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 应用的不同部件的设置不彼此依赖或相互耦合。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-400">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="ed3b2-401">选项还提供验证配置数据的机制。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-401">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="ed3b2-402">有关详细信息，请参阅[选项验证](#options-validation)部分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-402">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="ed3b2-403">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ed3b2-403">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ed3b2-404">系统必备</span><span class="sxs-lookup"><span data-stu-id="ed3b2-404">Prerequisites</span></span>

<span data-ttu-id="ed3b2-405">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="ed3b2-406">选项接口</span><span class="sxs-lookup"><span data-stu-id="ed3b2-406">Options interfaces</span></span>

<span data-ttu-id="ed3b2-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="ed3b2-409">更改通知</span><span class="sxs-lookup"><span data-stu-id="ed3b2-409">Change notifications</span></span>
* [<span data-ttu-id="ed3b2-410">命名选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-410">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="ed3b2-411">可重载配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-411">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="ed3b2-412">选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="ed3b2-412">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="ed3b2-413">[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-413">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="ed3b2-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="ed3b2-415">它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-415">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="ed3b2-416">默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-416">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="ed3b2-417">它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-417">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="ed3b2-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-419"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="ed3b2-420">可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-420">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="ed3b2-421">在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-421">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="ed3b2-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="ed3b2-423">有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-423">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="ed3b2-424"><xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-424"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="ed3b2-425">但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-425">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="ed3b2-426">你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-426">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="ed3b2-427">常规选项配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-427">General options configuration</span></span>

<span data-ttu-id="ed3b2-428">常规选项配置已作为示例 &num;1 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-428">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="ed3b2-429">选项类必须为包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-429">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="ed3b2-430">以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-430">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="ed3b2-431">设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-431">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="ed3b2-432">`Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-432">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="ed3b2-433">`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-433">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="ed3b2-434">以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-434">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="ed3b2-435">示例的 appsettings.json  文件指定 `option1` 和 `option2` 的值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-435">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="ed3b2-436">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-436">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="ed3b2-437">使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-437">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="ed3b2-438">通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-438">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="ed3b2-439">通过委托配置简单选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-439">Configure simple options with a delegate</span></span>

<span data-ttu-id="ed3b2-440">通过委托配置简单选项已作为示例 &num;2 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-440">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="ed3b2-441">使用委托设置选项值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-441">Use a delegate to set options values.</span></span> <span data-ttu-id="ed3b2-442">此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-442">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="ed3b2-443">在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-443">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ed3b2-444">它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-444">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="ed3b2-445">Index.cshtml.cs  :</span><span class="sxs-lookup"><span data-stu-id="ed3b2-445">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="ed3b2-446">可添加多个配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-446">You can add multiple configuration providers.</span></span> <span data-ttu-id="ed3b2-447">配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-447">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="ed3b2-448">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-448">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ed3b2-449">每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-449">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="ed3b2-450">在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json  中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-450">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="ed3b2-451">当启用多个配置服务时，指定的最后一个配置源优于其他源  ，由其设置配置值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-451">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="ed3b2-452">运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-452">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="ed3b2-453">子选项配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-453">Suboptions configuration</span></span>

<span data-ttu-id="ed3b2-454">子选项配置已作为示例 &num;3 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-454">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="ed3b2-455">应用应创建适用于应用中特定方案组（类）的选项类。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-455">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="ed3b2-456">需要配置值的部分应用应仅有权访问其使用的配置值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-456">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="ed3b2-457">将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-457">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="ed3b2-458">例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json  中的 `option1` 属性读取的键 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-458">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="ed3b2-459">在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-459">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="ed3b2-460">它将 `MySubOptions` 绑定到 appsettings.json  文件的 `subsection` 部分：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-460">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="ed3b2-461">`GetSection` 扩展方法需要 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-461">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="ed3b2-462">如果应用使用 [Microsoft.AspNetCore.App metapackage 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本），将自动包含此包。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-462">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="ed3b2-463">示例的 appsettings.json  文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-463">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="ed3b2-464">`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-464">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="ed3b2-465">页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs  )：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-465">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="ed3b2-466">运行应用时，`OnGet` 方法返回显示子选项类值的字符串：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-466">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="ed3b2-467">视图模型或通过直接视图注入提供的选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-467">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="ed3b2-468">视图模型或通过直接视图注入提供的选项已作为示例 &num;4 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-468">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="ed3b2-469">可在视图模型中或通过将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接注入到视图 (Pages/Index.cshtml.cs  ) 来提供选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-469">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="ed3b2-470">示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-470">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="ed3b2-471">当应用运行时，选项值显示在呈现的页面中：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-471">When the app is run, the options values are shown in the rendered page:</span></span>

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="ed3b2-473">通过 IOptionsSnapshot 重新加载配置数据</span><span class="sxs-lookup"><span data-stu-id="ed3b2-473">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="ed3b2-474">通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 &num;5 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-474">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="ed3b2-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支持包含最小处理开销的重新加载选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="ed3b2-476">针对请求生存期访问和缓存选项时，每个请求只能计算一次选项。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-476">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="ed3b2-477">以下示例演示如何在更改 appsettings.json  (Pages/Index.cshtml.cs  ) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-477">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="ed3b2-478">在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json  文件提供的常数值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-478">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="ed3b2-479">下图显示从 appsettings.json  文件加载的初始 `option1` 和 `option2` 值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-479">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="ed3b2-480">将 appsettings.json  文件中的值更改为 `value1_from_json UPDATED` 和 `200`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-480">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="ed3b2-481">保存 appsettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-481">Save the *appsettings.json* file.</span></span> <span data-ttu-id="ed3b2-482">刷新浏览器，查看更新的选项值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-482">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="ed3b2-483">包含 IConfigureNamedOptions 的命名选项支持</span><span class="sxs-lookup"><span data-stu-id="ed3b2-483">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="ed3b2-484">包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 &num;6 在示例应用中进行了演示。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-484">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="ed3b2-485">命名选项  支持允许应用在命名选项配置之间进行区分。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-485">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="ed3b2-486">在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用扩展方法 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-486">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="ed3b2-487">示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs  ) 访问命名选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-487">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="ed3b2-488">运行示例应用，将返回命名选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-488">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="ed3b2-489">从配置中提供从 appsettings.json  文件中加载的 `named_options_1` 值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-489">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="ed3b2-490">通过以下内容提供 `named_options_2` 值：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-490">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="ed3b2-491">针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-491">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="ed3b2-492">`MyOptions` 类提供的 `Option2` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-492">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="ed3b2-493">使用 ConfigureAll 方法配置所有选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-493">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="ed3b2-494">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-494">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="ed3b2-495">以下代码将针对包含公共值的所有配置实例配置 `Option1`。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-495">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="ed3b2-496">将以下代码手动添加到 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-496">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="ed3b2-497">添加代码后运行示例应用将产生以下结果：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-497">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="ed3b2-498">所有选项都是命名实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-498">All options are named instances.</span></span> <span data-ttu-id="ed3b2-499">现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向为 `string.Empty` 的 `Options.DefaultName` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-499">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="ed3b2-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="ed3b2-501"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-501">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="ed3b2-502">`null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-502">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="ed3b2-503">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="ed3b2-503">OptionsBuilder API</span></span>

<span data-ttu-id="ed3b2-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="ed3b2-505">`OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-505">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="ed3b2-506">选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-506">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="ed3b2-507">使用 DI 服务配置选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-507">Use DI services to configure options</span></span>

<span data-ttu-id="ed3b2-508">在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-508">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="ed3b2-509">将配置委托传递给 [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-509">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="ed3b2-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="ed3b2-511">创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-511">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="ed3b2-512">建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-512">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="ed3b2-513">在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-513">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="ed3b2-514">调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-514">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="ed3b2-515">选项后期配置</span><span class="sxs-lookup"><span data-stu-id="ed3b2-515">Options post-configuration</span></span>

<span data-ttu-id="ed3b2-516">使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-516">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="ed3b2-517">进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-517">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ed3b2-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="ed3b2-519">使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：</span><span class="sxs-lookup"><span data-stu-id="ed3b2-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="ed3b2-520">在启动期间访问选项</span><span class="sxs-lookup"><span data-stu-id="ed3b2-520">Accessing options during startup</span></span>

<span data-ttu-id="ed3b2-521"><xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-521"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="ed3b2-522">不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-522">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ed3b2-523">由于服务注册的顺序，可能存在不一致的选项状态。</span><span class="sxs-lookup"><span data-stu-id="ed3b2-523">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ed3b2-524">其他资源</span><span class="sxs-lookup"><span data-stu-id="ed3b2-524">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
