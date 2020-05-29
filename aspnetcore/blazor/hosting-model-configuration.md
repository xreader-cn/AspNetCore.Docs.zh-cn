---
<span data-ttu-id="f2e9d-101">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-103">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-104">'Identity'</span></span>
- <span data-ttu-id="f2e9d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-106">'Razor'</span></span>
- <span data-ttu-id="f2e9d-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="f2e9d-108">ASP.NET Core Blazor 托管模型配置</span><span class="sxs-lookup"><span data-stu-id="f2e9d-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="f2e9d-109">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f2e9d-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f2e9d-110">本文介绍了托管模型配置。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="f2e9d-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f2e9d-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="f2e9d-112">环境</span><span class="sxs-lookup"><span data-stu-id="f2e9d-112">Environment</span></span>

<span data-ttu-id="f2e9d-113">在本地运行应用时，环境默认为开发环境。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="f2e9d-114">发布应用时，环境默认为生产环境。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="f2e9d-115">托管的 Blazor WebAssembly 应用会通过中间件从服务器中提取环境，该中间件通过添加 `blazor-environment` 标头将该环境传达给浏览器。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="f2e9d-116">标头的值就是环境。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-116">The value of the header is the environment.</span></span> <span data-ttu-id="f2e9d-117">托管的 Blazor 应用和服务器应用共享同一个环境。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="f2e9d-118">有关详细信息（包括如何配置环境），请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f2e9d-119">对于在本地运行的独立应用，开发服务器会添加 `blazor-environment` 标头来指定开发环境。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="f2e9d-120">要为其他宿主环境指定环境，请添加 `blazor-environment` 标头。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="f2e9d-121">在下面的 IIS 示例中，将自定义标头添加到已发布的 web.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="f2e9d-122">web.config 文件位于 bin/Release/{TARGET FRAMEWORK}/publish 文件夹中 ：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="f2e9d-123">要对 IIS 使用在将应用发布到 publish 文件夹时不会被覆盖的自定义 web.config 文件，请参阅 <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> 。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="f2e9d-124">通过注入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 并读取 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 属性，在组件中获取应用的环境：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="f2e9d-125">在启动过程中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> 会通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 属性公开 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>，这让开发人员能够在其代码中拥有环境特定的逻辑：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="f2e9d-126">通过下面的便捷扩展方法，可在当前环境中检查开发环境、生产环境、暂存环境和自定义环境名称：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="f2e9d-127">如果 <xref:Microsoft.AspNetCore.Components.NavigationManager> 服务不可用，则启动期间可使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 属性。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="f2e9d-128">Configuration</span><span class="sxs-lookup"><span data-stu-id="f2e9d-128">Configuration</span></span>

Blazor<span data-ttu-id="f2e9d-129"> WebAssembly 加载以下来源的配置：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="f2e9d-130">应用设置文件（默认）：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-130">App settings files by default:</span></span>
  * <span data-ttu-id="f2e9d-131">wwwroot/appsettings.json</span><span class="sxs-lookup"><span data-stu-id="f2e9d-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="f2e9d-132">wwwroot/appsettings.{ENVIRONMENT}.json</span><span class="sxs-lookup"><span data-stu-id="f2e9d-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="f2e9d-133">应用注册的其他 [配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="f2e9d-134">并非所有提供程序都适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="f2e9d-135">Blazor WASM 的[ Clarify 配置提供程序 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) 会跟踪有关 Blazor WebAssembly 所支持提供程序的说明。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="f2e9d-136">Blazor WebAssembly 应用中的配置对用户可见。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="f2e9d-137">请勿在配置中存储应用机密或凭据。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="f2e9d-138">有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="f2e9d-139">应用设置配置</span><span class="sxs-lookup"><span data-stu-id="f2e9d-139">App settings configuration</span></span>

<span data-ttu-id="f2e9d-140">wwwroot/appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="f2e9d-141">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="f2e9d-142">提供程序配置</span><span class="sxs-lookup"><span data-stu-id="f2e9d-142">Provider configuration</span></span>

<span data-ttu-id="f2e9d-143">以下示例使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 提供其他配置：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="f2e9d-144">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-144">`Program.Main`:</span></span>

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

<span data-ttu-id="f2e9d-145">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="f2e9d-146">若要将 wwwroot 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="f2e9d-147">使用此方法时，现有 <xref:System.Net.Http.HttpClient> 服务注册可以使用创建的本地客户端来读取文件，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="f2e9d-148">wwwroot/cars.json：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="f2e9d-149">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="f2e9d-150">身份验证配置</span><span class="sxs-lookup"><span data-stu-id="f2e9d-150">Authentication configuration</span></span>

<span data-ttu-id="f2e9d-151">wwwroot/appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="f2e9d-152">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="f2e9d-153">日志记录配置</span><span class="sxs-lookup"><span data-stu-id="f2e9d-153">Logging configuration</span></span>

<span data-ttu-id="f2e9d-154">wwwroot/appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="f2e9d-155">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="f2e9d-156">主机生成器配置</span><span class="sxs-lookup"><span data-stu-id="f2e9d-156">Host builder configuration</span></span>

<span data-ttu-id="f2e9d-157">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="f2e9d-158">缓存的配置</span><span class="sxs-lookup"><span data-stu-id="f2e9d-158">Cached configuration</span></span>

<span data-ttu-id="f2e9d-159">配置文件会缓存以供脱机使用。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="f2e9d-160">使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="f2e9d-161">在部署之间编辑配置文件不起作用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="f2e9d-162">用户已拥有继续使用的文件的缓存版本。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="f2e9d-163">PWA 的 service-worker.js 和 service-worker-assets.js 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署 。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="f2e9d-164">有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="f2e9d-165">Logging</span><span class="sxs-lookup"><span data-stu-id="f2e9d-165">Logging</span></span>

<span data-ttu-id="f2e9d-166">要了解 Blazor WebAssembly 日志记录支持，请参阅 <xref:fundamentals/logging/index#create-logs-in-blazor>。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="f2e9d-167"> 服务器</span><span class="sxs-lookup"><span data-stu-id="f2e9d-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="f2e9d-168">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="f2e9d-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="f2e9d-169">如果客户端检测到连接已丢失，在客户端尝试重新连接时会向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="f2e9d-170">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="f2e9d-171">若要自定义 UI，请在 _Host.cshtml Razor 页面的 `<body>` 中定义一个 `id` 为 `components-reconnect-modal` 的元素：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="f2e9d-172">下表介绍应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="f2e9d-173">CSS 类</span><span class="sxs-lookup"><span data-stu-id="f2e9d-173">CSS class</span></span>                       | <span data-ttu-id="f2e9d-174">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="f2e9d-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="f2e9d-175">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-177">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-178">'Identity'</span></span>
- <span data-ttu-id="f2e9d-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-180">'Razor'</span></span>
- <span data-ttu-id="f2e9d-181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-182">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-184">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-185">'Identity'</span></span>
- <span data-ttu-id="f2e9d-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-187">'Razor'</span></span>
- <span data-ttu-id="f2e9d-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-189">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-191">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-192">'Identity'</span></span>
- <span data-ttu-id="f2e9d-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-194">'Razor'</span></span>
- <span data-ttu-id="f2e9d-195">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-196">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-198">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-199">'Identity'</span></span>
- <span data-ttu-id="f2e9d-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-201">'Razor'</span></span>
- <span data-ttu-id="f2e9d-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-203">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-205">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-206">'Identity'</span></span>
- <span data-ttu-id="f2e9d-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-208">'Razor'</span></span>
- <span data-ttu-id="f2e9d-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-210">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-212">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-213">'Identity'</span></span>
- <span data-ttu-id="f2e9d-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-215">'Razor'</span></span>
- <span data-ttu-id="f2e9d-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-217">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-219">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-220">'Identity'</span></span>
- <span data-ttu-id="f2e9d-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-222">'Razor'</span></span>
- <span data-ttu-id="f2e9d-223">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-224">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-226">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-227">'Identity'</span></span>
- <span data-ttu-id="f2e9d-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-229">'Razor'</span></span>
- <span data-ttu-id="f2e9d-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-231">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-233">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-234">'Identity'</span></span>
- <span data-ttu-id="f2e9d-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-236">'Razor'</span></span>
- <span data-ttu-id="f2e9d-237">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-238">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-240">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-241">'Identity'</span></span>
- <span data-ttu-id="f2e9d-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-243">'Razor'</span></span>
- <span data-ttu-id="f2e9d-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-245">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-247">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-248">'Identity'</span></span>
- <span data-ttu-id="f2e9d-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-250">'Razor'</span></span>
- <span data-ttu-id="f2e9d-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-252">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-254">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-255">'Identity'</span></span>
- <span data-ttu-id="f2e9d-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-257">'Razor'</span></span>
- <span data-ttu-id="f2e9d-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-259">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-261">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-262">'Identity'</span></span>
- <span data-ttu-id="f2e9d-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-264">'Razor'</span></span>
- <span data-ttu-id="f2e9d-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-265">'SignalR' uid:</span></span> 

<span data-ttu-id="f2e9d-266">---------------- | --- title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-268">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-269">'Identity'</span></span>
- <span data-ttu-id="f2e9d-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-271">'Razor'</span></span>
- <span data-ttu-id="f2e9d-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-273">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-275">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-276">'Identity'</span></span>
- <span data-ttu-id="f2e9d-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-278">'Razor'</span></span>
- <span data-ttu-id="f2e9d-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-280">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-282">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-283">'Identity'</span></span>
- <span data-ttu-id="f2e9d-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-285">'Razor'</span></span>
- <span data-ttu-id="f2e9d-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-287">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-289">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-290">'Identity'</span></span>
- <span data-ttu-id="f2e9d-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-292">'Razor'</span></span>
- <span data-ttu-id="f2e9d-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-294">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-296">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-297">'Identity'</span></span>
- <span data-ttu-id="f2e9d-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-299">'Razor'</span></span>
- <span data-ttu-id="f2e9d-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f2e9d-301">title:“ASP.NET Core Blazor 托管模型配置”author: description:“了解 Blazor 托管模型配置，包括如何将 Razor 组件集成到 Razor Pages 和 MVC 应用。”</span><span class="sxs-lookup"><span data-stu-id="f2e9d-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="f2e9d-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f2e9d-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-303">'Blazor'</span></span>
- <span data-ttu-id="f2e9d-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-304">'Identity'</span></span>
- <span data-ttu-id="f2e9d-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="f2e9d-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f2e9d-306">'Razor'</span></span>
- <span data-ttu-id="f2e9d-307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f2e9d-307">'SignalR' uid:</span></span> 

<span data-ttu-id="f2e9d-308">--------- | | `components-reconnect-show`     | 断开连接。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="f2e9d-309">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="f2e9d-310">显示模式。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-310">Show the modal.</span></span> <span data-ttu-id="f2e9d-311">| | `components-reconnect-hide`     | 将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="f2e9d-312">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-312">Hide the modal.</span></span> <span data-ttu-id="f2e9d-313">| | `components-reconnect-failed`   | 重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="f2e9d-314">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="f2e9d-315">| | `components-reconnect-rejected` | 已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="f2e9d-316">已达到服务器，但拒绝连接，服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="f2e9d-317">若要重载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="f2e9d-318">当出现以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="f2e9d-319">服务器端线路发生故障。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="f2e9d-320">客户端断开连接的时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="f2e9d-321">已释放用户正在与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="f2e9d-322">服务器已重启，或者应用的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="f2e9d-323">呈现模式</span><span class="sxs-lookup"><span data-stu-id="f2e9d-323">Render mode</span></span>

<span data-ttu-id="f2e9d-324">默认情况下，Blazor 服务器应用设置为：在客户端与服务器建立连接之前在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-324">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="f2e9d-325">这是在 _Host.cshtml Razor 页面中设置的：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="f2e9d-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f2e9d-327">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="f2e9d-328">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="f2e9d-329">描述</span><span class="sxs-lookup"><span data-stu-id="f2e9d-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f2e9d-330">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f2e9d-331">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f2e9d-332">呈现 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f2e9d-333">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-333">Output from the component isn't included.</span></span> <span data-ttu-id="f2e9d-334">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f2e9d-335">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="f2e9d-336">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="f2e9d-337">为 Blazor 服务器应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="f2e9d-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="f2e9d-338">有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="f2e9d-339">例如，可能需要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="f2e9d-340">若要在 Pages/_Host.cshtml 文件中配置 SignalR 客户端，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f2e9d-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="f2e9d-341">将 `autostart="false"` 属性添加到 `blazor.server.js` 脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="f2e9d-342">调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="f2e9d-343">Logging</span><span class="sxs-lookup"><span data-stu-id="f2e9d-343">Logging</span></span>

<span data-ttu-id="f2e9d-344">有关 Blazor 服务器日志记录支持的信息，请参阅 <xref:fundamentals/logging/index#create-logs-in-blazor>。</span><span class="sxs-lookup"><span data-stu-id="f2e9d-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
