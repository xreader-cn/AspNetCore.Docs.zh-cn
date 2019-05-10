---
title: ASP.NET Core 中的选项模式
author: guardrex
description: 了解如何使用选项模式来表示 ASP.NET Core 应用中的相关设置组。
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 5cf84cd601fb3559da559d2bd633391841fe8a19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888642"
---
# <a name="options-pattern-in-aspnet-core"></a>ASP.NET Core 中的选项模式

作者：[Luke Latham](https://github.com/guardrex)

::: moniker range="<= aspnetcore-1.1"

对于本主题的 1.1 版本，请下载 [ASP.NET Core（版本 1.1，PDF）中的选项模式](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Options_1.1.pdf)。

::: moniker-end

选项模式使用类来表示相关设置的组。 当[配置设置](xref:fundamentals/configuration/index)由方案隔离到单独的类时，应用遵循两个重要软件工程原则：

* [接口分离原则 (ISP) 或封装](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; 依赖于配置设置的方案（类）仅依赖于其使用的配置设置。
* [关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; 应用的不同部件的设置不彼此依赖或相互耦合。

选项还提供验证配置数据的机制。 有关详细信息，请参阅[选项验证](#options-validation)部分。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 包。

## <a name="options-interfaces"></a>选项接口

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于检索选项并管理 `TOptions` 实例的选项通知。 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 支持以下方案：

* 更改通知
* [命名选项](#named-options-support-with-iconfigurenamedoptions)
* [可重载配置](#reload-configuration-data-with-ioptionssnapshot)
* 选择性选项失效 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)

[后期配置](#options-post-configuration)方案允许你在进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后设置或更改选项。

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> 负责新建选项实例。 它具有单个 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 方法。 默认实现采用所有已注册 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 和 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 并首先运行所有配置，然后才进行后期配置。 它区分 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 和 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 且仅调用适当的接口。

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 由 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 用于缓存 `TOptions` 实例。 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> 可使监视器中的选项实例无效，以便重新计算值 (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>)。 可以通过 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> 手动引入值。 在应按需重新创建所有命名实例时使用 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 方法。

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 在每次请求时应重新计算选项的方案中有用。 有关详细信息，请参阅[通过 IOptionsSnapshot 重新加载配置数据](#reload-configuration-data-with-ioptionssnapshot)部分。

<xref:Microsoft.Extensions.Options.IOptions%601> 可用于支持选项。 但是，<xref:Microsoft.Extensions.Options.IOptions%601> 不支持前面的 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 方案。 你可以在已使用 <xref:Microsoft.Extensions.Options.IOptions%601> 接口的现有框架和库中继续使用 <xref:Microsoft.Extensions.Options.IOptions%601>，并且不需要 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 提供的方案。

## <a name="general-options-configuration"></a>常规选项配置

常规选项配置已作为示例 &num;1 在示例应用中进行了演示。

选项类必须为包含公共无参数构造函数的非抽象类。 以下类 `MyOptions` 具有两种属性：`Option1` 和 `Option2`。 设置默认值为可选，但以下示例中的类构造函数设置了 `Option1` 的默认值。 `Option2` 具有通过直接初始化属性设置的默认值 (Models/MyOptions.cs)：

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` 类已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 添加到服务容器并绑定到配置：

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

以下页面模型通过 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 使用[构造函数依赖关系注入](xref:mvc/controllers/dependency-injection)来访问设置 (Pages/Index.cshtml.cs)：

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

示例的 appsettings.json 文件指定 `option1` 和 `option2` 的值：

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> 使用自定义 <xref:System.Configuration.ConfigurationBuilder> 从设置文件加载选项配置时，请确认基路径设置正确：
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
> 通过 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 从设置文件加载选项配置时，不需要显式设置基路径。

## <a name="configure-simple-options-with-a-delegate"></a>通过委托配置简单选项

通过委托配置简单选项已作为示例 &num;2 在示例应用中进行了演示。

使用委托设置选项值。 此示例应用使用 `MyOptionsWithDelegateConfig` 类 (Models/MyOptionsWithDelegateConfig.cs)：

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

在以下代码中，已向服务容器添加第二个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。 它通过 `MyOptionsWithDelegateConfig` 使用委托来配置绑定：

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

Index.cshtml.cs:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

可添加多个配置提供程序。 配置提供程序可从 NuGet 包中获取，并按照注册的顺序应用。 有关更多信息，请参见<xref:fundamentals/configuration/index>。

每次调用 <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> 都会将 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务添加到服务容器。 在前面的示例中，`Option1` 和 `Option2` 的值同时在 appsettings.json 中指定，但 `Option1` 和 `Option2` 的值被配置的委托替代。

当启用多个配置服务时，指定的最后一个配置源优于其他源，由其设置配置值。 运行应用时，页面模型的 `OnGet` 方法返回显示选项类值的字符串：

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>子选项配置

子选项配置已作为示例 &num;3 在示例应用中进行了演示。

应用应创建适用于应用中特定方案组（类）的选项类。 需要配置值的部分应用应仅有权访问其使用的配置值。

将选项绑定到配置时，选项类型中的每个属性都将绑定到窗体 `property[:sub-property:]` 的配置键。 例如，`MyOptions.Option1` 属性将绑定到从 appsettings.json 中的 `option1` 属性读取的键 `Option1`。

在以下代码中，已向服务容器添加第三个 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。 它将 `MySubOptions` 绑定到 appsettings.json 文件的 `subsection` 部分：

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` 扩展方法需要 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet 包。 如果应用使用 [Microsoft.AspNetCore.App metapackage 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本），将自动包含此包。

示例的 appsettings.json 文件定义具有 `suboption1` 和 `suboption2` 的键的 `subsection` 成员：

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` 类将属性 `SubOption1` 和 `SubOption2` 定义为保留选项值 (Models/MySubOptions.cs)：

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

页面模型的 `OnGet` 方法返回包含选项值的字符串 (Pages/Index.cshtml.cs)：

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

运行应用时，`OnGet` 方法返回显示子选项类值的字符串：

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>视图模型或通过直接视图注入提供的选项

视图模型或通过直接视图注入提供的选项已作为示例 &num;4 在示例应用中进行了演示。

可在视图模型中或通过将 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 直接注入到视图 (Pages/Index.cshtml.cs) 来提供选项：

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

示例应用演示如何使用 `@inject` 指令注入 `IOptionsMonitor<MyOptions>`：

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

当应用运行时，选项值显示在呈现的页面中：

![选项值 Option1：value1_from_json 和 Option2：-1 从模型中加载并注入视图。](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>通过 IOptionsSnapshot 重新加载配置数据

通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 重新加载配置数据已作为示例 &num;5 在示例应用中进行了演示。

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 支持包含最小处理开销的重新加载选项。

针对请求生存期访问和缓存选项时，每个请求只能计算一次选项。

以下示例演示如何在更改 appsettings.json (Pages/Index.cshtml.cs) 后创建新的 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>。 在更改文件和重新加载配置之前，针对服务器的多个请求返回 appsettings.json 文件提供的常数值。

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

下图显示从 appsettings.json 文件加载的初始 `option1` 和 `option2` 值：

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

将 appsettings.json 文件中的值更改为 `value1_from_json UPDATED` 和 `200`。 保存 appsettings.json 文件。 刷新浏览器，查看更新的选项值：

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>包含 IConfigureNamedOptions 的命名选项支持

包含 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的命名选项支持已作为示例 &num;6 在示例应用中进行了演示。

命名选项支持允许应用在命名选项配置之间进行区分。 在示例应用中，命名选项通过 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) 进行声明，其调用扩展方法 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*)：

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

示例应用通过 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (Pages/Index.cshtml.cs) 访问命名选项：

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

运行示例应用，将返回命名选项：

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

从配置中提供从 appsettings.json 文件中加载的 `named_options_1` 值。 通过以下内容提供 `named_options_2` 值：

* 针对 `Option1` 的 `ConfigureServices` 中的 `named_options_2` 委托。
* `MyOptions` 类提供的 `Option2` 的默认值。

## <a name="configure-all-options-with-the-configureall-method"></a>使用 ConfigureAll 方法配置所有选项

使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 方法配置所有选项实例。 以下代码将针对包含公共值的所有配置实例配置 `Option1`。 将以下代码手动添加到 `Startup.ConfigureServices` 方法：

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

添加代码后运行示例应用将产生以下结果：

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> 所有选项都是命名实例。 现有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 实例将被视为面向为 `string.Empty` 的 `Options.DefaultName` 实例。 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 还可实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>。 <xref:Microsoft.Extensions.Options.IOptionsFactory%601> 的默认实现具有适当地使用每个实例的逻辑。 `null` 命名选项用于面向所有命名实例而不是某一特定命名实例（<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 和 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 使用此约定）。

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> 用于配置 `TOptions` 实例。 `OptionsBuilder` 简化了创建命名选项的过程，因为它只是初始 `AddOptions<TOptions>(string optionsName)` 调用的单个参数，而不会出现在所有后续调用中。 选项验证和接受服务依赖关系的 `ConfigureOptions` 重载仅可通过 `OptionsBuilder` 获得。

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>使用 DI 服务配置选项

在配置选项时，可以通过以下两种方式通过依赖关系注入访问其他服务：

* 将配置委托传递给 [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 上的 [ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)。 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) 提供 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 的重载，该重载允许使用最多五个服务来配置选项：

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* 创建实现 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 或 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> 的你自己的类型，并将该类型注册为服务。

建议将配置委托传递给 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)，因为创建服务较复杂。 在使用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)时，创建你自己的类型等效于框架为你执行的操作。 调用[ Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) 会注册临时泛型 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>，它具有接受指定的泛型服务类型的构造函数。 

::: moniker range=">= aspnetcore-2.2"

## <a name="options-validation"></a>选项验证

借助选项验证，可以验证已配置的选项。 使用验证方法调用 `Validate`。如果选项有效，方法返回 `true`；如果无效，方法返回 `false`：

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

上面的示例将命名选项实例设置为 `optionalOptionsName`。 默认选项实例为 `Options.DefaultName`。

选项验证在选项实例创建后运行。 系统保证在选项实例首次获得访问时通过验证。

> [!IMPORTANT]
> 选项验证无法防止在最初配置和验证选项后发生选项修改。

`Validate` 方法接受 `Func<TOptions, bool>`。 若要完全自定义验证，请实现 `IValidateOptions<TOptions>`，它支持：

* 验证多种类型的选项：`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* 验证取决于其他选项类型：`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` 验证：

* 特定的命名选项实例。
* 所有选项（如果 `name` 为 `null` 的话）。

通过接口实现返回 `ValidateOptionsResult`：

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

通过调用 `OptionsBuilder<TOptions>` 上的 `ValidateDataAnnotations` 方法，可以从 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 包中获得基于数据注释的验证。 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.2 或更高版本）中包括 `Microsoft.Extensions.Options.DataAnnotations`。

```csharp
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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

设计团队正在考虑为未来版本提供预先验证（在启动时快速失败）。

::: moniker-end

## <a name="options-post-configuration"></a>选项后期配置

使用 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> 设置后期配置。 进行所有 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 配置后运行后期配置：

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> 可用于对命名选项进行后期配置：

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

使用 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> 对所有配置实例进行后期配置：

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>在启动期间访问选项

<xref:Microsoft.Extensions.Options.IOptions%601> 和 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> 可用于 `Startup.Configure` 中，因为在 `Configure` 方法执行之前已生成服务。

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

不使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.Options.IOptions%601> 或 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>。 由于服务注册的顺序，可能存在不一致的选项状态。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/configuration/index>
