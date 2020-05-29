---
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET Core Blazor 托管模型配置

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

本文介绍了托管模型配置。

## <a name="blazor-webassembly"></a>Blazor WebAssembly

### <a name="environment"></a>环境

在本地运行应用时，环境默认为开发环境。 发布应用时，环境默认为生产环境。

托管的 Blazor WebAssembly 应用会通过中间件从服务器中提取环境，该中间件通过添加 `blazor-environment` 标头将该环境传达给浏览器。 标头的值就是环境。 托管的 Blazor 应用和服务器应用共享同一个环境。 有关详细信息（包括如何配置环境），请参阅 <xref:fundamentals/environments>。

对于在本地运行的独立应用，开发服务器会添加 `blazor-environment` 标头来指定开发环境。 要为其他宿主环境指定环境，请添加 `blazor-environment` 标头。

在下面的 IIS 示例中，将自定义标头添加到已发布的 web.config 文件中。 web.config 文件位于 bin/Release/{TARGET FRAMEWORK}/publish 文件夹中 ：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> 要对 IIS 使用在将应用发布到 publish 文件夹时不会被覆盖的自定义 web.config 文件，请参阅 <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> 。

通过注入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 并读取 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 属性，在组件中获取应用的环境：

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

在启动过程中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> 会通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 属性公开 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>，这让开发人员能够在其代码中拥有环境特定的逻辑：

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

通过下面的便捷扩展方法，可在当前环境中检查开发环境、生产环境、暂存环境和自定义环境名称：

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

如果 <xref:Microsoft.AspNetCore.Components.NavigationManager> 服务不可用，则启动期间可使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 属性。

### <a name="configuration"></a>Configuration

Blazor WebAssembly 加载以下来源的配置：

* 应用设置文件（默认）：
  * wwwroot/appsettings.json
  * wwwroot/appsettings.{ENVIRONMENT}.json
* 应用注册的其他 [配置提供程序](xref:fundamentals/configuration/index)。 并非所有提供程序都适用于 Blazor WebAssembly 应用。 Blazor WASM 的[ Clarify 配置提供程序 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) 会跟踪有关 Blazor WebAssembly 所支持提供程序的说明。

> [!WARNING]
> Blazor WebAssembly 应用中的配置对用户可见。 请勿在配置中存储应用机密或凭据。

有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。

#### <a name="app-settings-configuration"></a>应用设置配置

wwwroot/appsettings.json：

```json
{
  "message": "Hello from config!"
}
```

将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>提供程序配置

以下示例使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 提供其他配置：

`Program.Main`：

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

若要将 wwwroot 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。 使用此方法时，现有 <xref:System.Net.Http.HttpClient> 服务注册可以使用创建的本地客户端来读取文件，如以下示例所示：

wwwroot/cars.json：

```json
{
    "size": "tiny"
}
```

`Program.Main`：

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a>身份验证配置

wwwroot/appsettings.json：

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`：

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a>日志记录配置

wwwroot/appsettings.json：

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`：

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>主机生成器配置

`Program.Main`：

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>缓存的配置

配置文件会缓存以供脱机使用。 使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。 在部署之间编辑配置文件不起作用，原因如下：

* 用户已拥有继续使用的文件的缓存版本。
* PWA 的 service-worker.js 和 service-worker-assets.js 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署 。

有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。

### <a name="logging"></a>Logging

要了解 Blazor WebAssembly 日志记录支持，请参阅 <xref:fundamentals/logging/index#create-logs-in-blazor>。

## <a name="blazor-server"></a>Blazor 服务器

### <a name="reflect-the-connection-state-in-the-ui"></a>反映 UI 中的连接状态

如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。 如果重新连接失败，则会向用户提供重试选项。

若要自定义 UI，请在 _Host.cshtml Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。

| CSS 类                       | 指示&hellip; |
| ---
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---------------- | --- title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

--------- | | `components-reconnect-show`     | 断开连接。 客户端正在尝试重新连接。 显示模式。 | | `components-reconnect-hide`     | 将为服务器重新建立活动连接。 隐藏模式。 | | `components-reconnect-failed`   | 重新连接失败，可能是由于网络故障引起的。 若要尝试重新连接，请调用 `window.Blazor.reconnect()`。 | | `components-reconnect-rejected` | 已拒绝重新连接。 已达到服务器，但拒绝连接，服务器上的用户状态丢失。 若要重载应用，请调用 `location.reload()`。 当出现以下情况时，可能会导致此连接状态：<ul><li>服务器端线路发生故障。</li><li>客户端断开连接的时间足以使服务器删除用户的状态。 已释放用户正在与之交互的组件的实例。</li><li>服务器已重启，或者应用的工作进程被回收。</li></ul> |

### <a name="render-mode"></a>呈现模式

默认情况下，Blazor 服务器应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。 这是在 _Host.cshtml Razor 页面中设置的：

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 配置组件是否：

* 在页面中预呈现。
* 在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | 描述 |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | 将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | 呈现 Blazor 服务器应用的标记。 不包括组件的输出。 用户代理启动时，此标记用于启动 Blazor 应用。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | 将组件呈现为静态 HTML。 |

不支持从静态 HTML 页面呈现服务器组件。

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>为 Blazor 服务器应用配置 SignalR 客户端

有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。 例如，可能需要在 SignalR 客户端上配置日志记录以诊断连接问题。

若要在 Pages/_Host.cshtml 文件中配置 SignalR 客户端，请执行以下操作：

* 将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。
* 调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a>Logging

有关 Blazor 服务器日志记录支持的信息，请参阅 <xref:fundamentals/logging/index#create-logs-in-blazor>。
