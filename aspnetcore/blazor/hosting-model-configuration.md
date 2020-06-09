---
title: "title:'ASP.NET Core Blazor 托管模型配置' author: guardrex description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date:2020 年 5 月 28 日 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: e3b8b91a570210e77f307c49f7be21eeab714daa
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355105"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="8e858-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8e858-103">'Blazor'</span></span>

<span data-ttu-id="8e858-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8e858-104">'Identity'</span></span>

<span data-ttu-id="8e858-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8e858-105">'Let's Encrypt'</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="8e858-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8e858-106">'Razor'</span></span>

### <a name="environment"></a><span data-ttu-id="8e858-107">'SignalR' uid: blazor/hosting-model-configuration</span><span class="sxs-lookup"><span data-stu-id="8e858-107">'SignalR' uid: blazor/hosting-model-configuration</span></span>

<span data-ttu-id="8e858-108">ASP.NET Core Blazor 托管模型配置</span><span class="sxs-lookup"><span data-stu-id="8e858-108">ASP.NET Core Blazor hosting model configuration</span></span> <span data-ttu-id="8e858-109">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8e858-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8e858-110">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="8e858-110">This article covers hosting model configuration.</span></span> Blazor<span data-ttu-id="8e858-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8e858-111"> WebAssembly</span></span> <span data-ttu-id="8e858-112">环境</span><span class="sxs-lookup"><span data-stu-id="8e858-112">Environment</span></span> <span data-ttu-id="8e858-113">在本地运行应用时，环境默认为开发环境。</span><span class="sxs-lookup"><span data-stu-id="8e858-113">When running an app locally, the environment defaults to Development.</span></span>

<span data-ttu-id="8e858-114">发布应用时，环境默认为生产环境。</span><span class="sxs-lookup"><span data-stu-id="8e858-114">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="8e858-115">托管的 Blazor WebAssembly 应用会通过中间件从服务器中提取环境，该中间件通过添加 `blazor-environment` 标头将该环境传达给浏览器。</span><span class="sxs-lookup"><span data-stu-id="8e858-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span>

<span data-ttu-id="8e858-116">标头的值就是环境。</span><span class="sxs-lookup"><span data-stu-id="8e858-116">The value of the header is the environment.</span></span> <span data-ttu-id="8e858-117">托管的 Blazor 应用和服务器应用共享同一个环境。</span><span class="sxs-lookup"><span data-stu-id="8e858-117">The hosted Blazor app and the server app share the same environment.</span></span>

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
> <span data-ttu-id="8e858-118">有关详细信息（包括如何配置环境），请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8e858-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8e858-119">对于在本地运行的独立应用，开发服务器会添加 `blazor-environment` 标头来指定开发环境。</span><span class="sxs-lookup"><span data-stu-id="8e858-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="8e858-120">要为其他宿主环境指定环境，请添加 `blazor-environment` 标头。</span><span class="sxs-lookup"><span data-stu-id="8e858-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="8e858-121">在下面的 IIS 示例中，将自定义标头添加到已发布的 web.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="8e858-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span>

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

<span data-ttu-id="8e858-122">web.config 文件位于 bin/Release/{TARGET FRAMEWORK}/publish 文件夹中 ：</span><span class="sxs-lookup"><span data-stu-id="8e858-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

### <a name="configuration"></a><span data-ttu-id="8e858-123">要对 IIS 使用在将应用发布到 publish 文件夹时不会被覆盖的自定义 web.config 文件，请参阅 <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="8e858-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="8e858-124">通过注入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 并读取 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 属性，在组件中获取应用的环境：</span><span class="sxs-lookup"><span data-stu-id="8e858-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

* <span data-ttu-id="8e858-125">在启动过程中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> 会通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 属性公开 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>，这让开发人员能够在其代码中拥有环境特定的逻辑：</span><span class="sxs-lookup"><span data-stu-id="8e858-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>
  * <span data-ttu-id="8e858-126">通过下面的便捷扩展方法，可在当前环境中检查开发环境、生产环境、暂存环境和自定义环境名称：</span><span class="sxs-lookup"><span data-stu-id="8e858-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>
  * <span data-ttu-id="8e858-127">如果 <xref:Microsoft.AspNetCore.Components.NavigationManager> 服务不可用，则启动期间可使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 属性。</span><span class="sxs-lookup"><span data-stu-id="8e858-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>
* <span data-ttu-id="8e858-128">Configuration</span><span class="sxs-lookup"><span data-stu-id="8e858-128">Configuration</span></span> Blazor<span data-ttu-id="8e858-129"> WebAssembly 加载以下来源的配置：</span><span class="sxs-lookup"><span data-stu-id="8e858-129"> WebAssembly loads configuration from:</span></span> <span data-ttu-id="8e858-130">应用设置文件（默认）：</span><span class="sxs-lookup"><span data-stu-id="8e858-130">App settings files by default:</span></span>

> [!WARNING]
> <span data-ttu-id="8e858-131">wwwroot/appsettings.json</span><span class="sxs-lookup"><span data-stu-id="8e858-131">*wwwroot/appsettings.json*</span></span> <span data-ttu-id="8e858-132">wwwroot/appsettings.{ENVIRONMENT}.json</span><span class="sxs-lookup"><span data-stu-id="8e858-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="8e858-133">应用注册的其他 [配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="8e858-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="8e858-134">并非所有提供程序都适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="8e858-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span>

<span data-ttu-id="8e858-135">Blazor WASM 的[ Clarify 配置提供程序 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) 会跟踪有关 Blazor WebAssembly 所支持提供程序的说明。</span><span class="sxs-lookup"><span data-stu-id="8e858-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="8e858-136">Blazor WebAssembly 应用中的配置对用户可见。</span><span class="sxs-lookup"><span data-stu-id="8e858-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="8e858-137">请勿在配置中存储应用机密或凭据。</span><span class="sxs-lookup"><span data-stu-id="8e858-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="8e858-138">有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="8e858-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="8e858-139">应用设置配置</span><span class="sxs-lookup"><span data-stu-id="8e858-139">App settings configuration</span></span>

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

<span data-ttu-id="8e858-140">wwwroot/appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="8e858-140">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="8e858-141">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="8e858-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span> <span data-ttu-id="8e858-142">提供程序配置</span><span class="sxs-lookup"><span data-stu-id="8e858-142">Provider configuration</span></span>

<span data-ttu-id="8e858-143">以下示例使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 提供其他配置：</span><span class="sxs-lookup"><span data-stu-id="8e858-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="8e858-144">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="8e858-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="8e858-145">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="8e858-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

<span data-ttu-id="8e858-146">若要将 wwwroot 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="8e858-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="8e858-147">使用此方法时，现有 <xref:System.Net.Http.HttpClient> 服务注册可以使用创建的本地客户端来读取文件，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="8e858-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="8e858-148">wwwroot/cars.json：</span><span class="sxs-lookup"><span data-stu-id="8e858-148">*wwwroot/cars.json*:</span></span>

<span data-ttu-id="8e858-149">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="8e858-149">`Program.Main`:</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="8e858-150">身份验证配置</span><span class="sxs-lookup"><span data-stu-id="8e858-150">Authentication configuration</span></span>

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

<span data-ttu-id="8e858-151">wwwroot/appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="8e858-151">*wwwroot/appsettings.json*:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="8e858-152">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="8e858-152">`Program.Main`:</span></span>

<span data-ttu-id="8e858-153">日志记录配置</span><span class="sxs-lookup"><span data-stu-id="8e858-153">Logging configuration</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="8e858-154">为 [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) 添加包引用：</span><span class="sxs-lookup"><span data-stu-id="8e858-154">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

<span data-ttu-id="8e858-155">wwwroot/appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="8e858-155">*wwwroot/appsettings.json*:</span></span> <span data-ttu-id="8e858-156">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="8e858-156">`Program.Main`:</span></span> <span data-ttu-id="8e858-157">主机生成器配置</span><span class="sxs-lookup"><span data-stu-id="8e858-157">Host builder configuration</span></span>

* <span data-ttu-id="8e858-158">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="8e858-158">`Program.Main`:</span></span>
* <span data-ttu-id="8e858-159">缓存的配置</span><span class="sxs-lookup"><span data-stu-id="8e858-159">Cached configuration</span></span>

<span data-ttu-id="8e858-160">配置文件会缓存以供脱机使用。</span><span class="sxs-lookup"><span data-stu-id="8e858-160">Configuration files are cached for offline use.</span></span>

### <a name="logging"></a><span data-ttu-id="8e858-161">使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。</span><span class="sxs-lookup"><span data-stu-id="8e858-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span>

<span data-ttu-id="8e858-162">在部署之间编辑配置文件不起作用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="8e858-162">Editing configuration files between deployments has no effect because:</span></span>

## <a name="blazor-server"></a><span data-ttu-id="8e858-163">用户已拥有继续使用的文件的缓存版本。</span><span class="sxs-lookup"><span data-stu-id="8e858-163">Users have cached versions of the files that they continue to use.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="8e858-164">PWA 的 service-worker.js 和 service-worker-assets.js 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署 。</span><span class="sxs-lookup"><span data-stu-id="8e858-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="8e858-165">有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。</span><span class="sxs-lookup"><span data-stu-id="8e858-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span> <span data-ttu-id="8e858-166">Logging</span><span class="sxs-lookup"><span data-stu-id="8e858-166">Logging</span></span>

<span data-ttu-id="8e858-167">要了解 Blazor WebAssembly 日志记录支持，请参阅 <xref:fundamentals/logging/index#create-logs-in-blazor>。</span><span class="sxs-lookup"><span data-stu-id="8e858-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Blazor<span data-ttu-id="8e858-168"> 服务器</span><span class="sxs-lookup"><span data-stu-id="8e858-168"> Server</span></span>

| <span data-ttu-id="8e858-169">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="8e858-169">Reflect the connection state in the UI</span></span>                       | <span data-ttu-id="8e858-170">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="8e858-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="8e858-171">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="8e858-171">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="8e858-172">若要自定义 UI，请在 _Host.cshtml Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：</span><span class="sxs-lookup"><span data-stu-id="8e858-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span> <span data-ttu-id="8e858-173">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="8e858-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="8e858-174">CSS 类</span><span class="sxs-lookup"><span data-stu-id="8e858-174">CSS class</span></span> <span data-ttu-id="8e858-175">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="8e858-175">Indicates&hellip;</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="8e858-176">连接已丢失。</span><span class="sxs-lookup"><span data-stu-id="8e858-176">A lost connection.</span></span> <span data-ttu-id="8e858-177">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="8e858-177">The client is attempting to reconnect.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="8e858-178">显示模式。</span><span class="sxs-lookup"><span data-stu-id="8e858-178">Show the modal.</span></span> <span data-ttu-id="8e858-179">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="8e858-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="8e858-180">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="8e858-180">Hide the modal.</span></span> <span data-ttu-id="8e858-181">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="8e858-181">Reconnection failed, probably due to a network failure.</span></span><ul><li><span data-ttu-id="8e858-182">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="8e858-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span></li><li><span data-ttu-id="8e858-183">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="8e858-183">Reconnection rejected.</span></span> <span data-ttu-id="8e858-184">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="8e858-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span></li><li><span data-ttu-id="8e858-185">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="8e858-185">To reload the app, call `location.reload()`.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="8e858-186">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="8e858-186">This connection state may result when:</span></span>

<span data-ttu-id="8e858-187">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="8e858-187">A crash in the server-side circuit occurs.</span></span> <span data-ttu-id="8e858-188">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="8e858-188">The client is disconnected long enough for the server to drop the user's state.</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="8e858-189">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="8e858-189">Instances of the components that the user is interacting with are disposed.</span></span>

* <span data-ttu-id="8e858-190">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="8e858-190">The server is restarted, or the app's worker process is recycled.</span></span>
* <span data-ttu-id="8e858-191">呈现模式</span><span class="sxs-lookup"><span data-stu-id="8e858-191">Render mode</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="8e858-192">默认情况下，Blazor 服务器应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="8e858-192">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="8e858-193">这是在 _Host.cshtml Razor 页面中设置的：</span><span class="sxs-lookup"><span data-stu-id="8e858-193">This is set up in the *_Host.cshtml* Razor page:</span></span> <span data-ttu-id="8e858-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="8e858-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="8e858-195">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="8e858-195">Is prerendered into the page.</span></span> <span data-ttu-id="8e858-196">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="8e858-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span> <span data-ttu-id="8e858-197">描述</span><span class="sxs-lookup"><span data-stu-id="8e858-197">Description</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="8e858-198">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="8e858-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> |

<span data-ttu-id="8e858-199">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="8e858-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="8e858-200">呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="8e858-200">Renders a marker for a Blazor Server app.</span></span>

<span data-ttu-id="8e858-201">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="8e858-201">Output from the component isn't included.</span></span> <span data-ttu-id="8e858-202">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="8e858-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span>

<span data-ttu-id="8e858-203">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="8e858-203">Renders the component into static HTML.</span></span>

* <span data-ttu-id="8e858-204">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="8e858-204">Rendering server components from a static HTML page isn't supported.</span></span>
* <span data-ttu-id="8e858-205">为 Blazor 服务器应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="8e858-205">Configure the SignalR client for Blazor Server apps</span></span>

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

### <a name="logging"></a><span data-ttu-id="8e858-206">有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="8e858-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span>

<span data-ttu-id="8e858-207">例如，可能需要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="8e858-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
