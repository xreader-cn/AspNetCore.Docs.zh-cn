---
title: ASP.NET Core Blazor 配置
author: guardrex
description: 了解 Blazor 应用的配置，包括应用设置、身份验证和日志记录配置。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 3e1d265f269e2a2c5ea584e62e80f8a6167f9d3a
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944401"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="83b81-103">ASP.NET Core Blazor 配置</span><span class="sxs-lookup"><span data-stu-id="83b81-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="83b81-104">本主题适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="83b81-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="83b81-105">若要获取 ASP.NET Core 应用配置的通用指南，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="83b81-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor WebAssembly<span data-ttu-id="83b81-106"> 加载以下来源的配置：</span><span class="sxs-lookup"><span data-stu-id="83b81-106"> loads configuration from:</span></span>

* <span data-ttu-id="83b81-107">应用设置文件（默认）：</span><span class="sxs-lookup"><span data-stu-id="83b81-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="83b81-108">应用注册的其他 [配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="83b81-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="83b81-109">并非所有提供程序都适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="83b81-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="83b81-110">Blazor WASM 的 [Clarify 配置提供程序 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) 会跟踪有关 Blazor WebAssembly 所支持提供程序的说明。</span><span class="sxs-lookup"><span data-stu-id="83b81-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="83b81-111">Blazor WebAssembly 应用中的配置对用户可见。</span><span class="sxs-lookup"><span data-stu-id="83b81-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="83b81-112">请勿在配置中存储应用机密或凭据。</span><span class="sxs-lookup"><span data-stu-id="83b81-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="83b81-113">有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="83b81-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="83b81-114">应用设置配置</span><span class="sxs-lookup"><span data-stu-id="83b81-114">App settings configuration</span></span>

<span data-ttu-id="83b81-115">`wwwroot/appsettings.json`：</span><span class="sxs-lookup"><span data-stu-id="83b81-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="83b81-116">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="83b81-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="83b81-117">提供程序配置</span><span class="sxs-lookup"><span data-stu-id="83b81-117">Provider configuration</span></span>

<span data-ttu-id="83b81-118">以下示例使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 提供其他配置：</span><span class="sxs-lookup"><span data-stu-id="83b81-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="83b81-119">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="83b81-119">`Program.Main`:</span></span>

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

<span data-ttu-id="83b81-120">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="83b81-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="83b81-121">若要将 `wwwroot` 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="83b81-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="83b81-122">使用此方法时，现有 <xref:System.Net.Http.HttpClient> 服务注册可以使用创建的本地客户端来读取文件，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="83b81-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="83b81-123">`wwwroot/cars.json`：</span><span class="sxs-lookup"><span data-stu-id="83b81-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="83b81-124">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="83b81-124">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="83b81-125">身份验证配置</span><span class="sxs-lookup"><span data-stu-id="83b81-125">Authentication configuration</span></span>

<span data-ttu-id="83b81-126">`wwwroot/appsettings.json`：</span><span class="sxs-lookup"><span data-stu-id="83b81-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="83b81-127">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="83b81-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="83b81-128">日志记录配置</span><span class="sxs-lookup"><span data-stu-id="83b81-128">Logging configuration</span></span>

<span data-ttu-id="83b81-129">添加 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) 的包引用：</span><span class="sxs-lookup"><span data-stu-id="83b81-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="83b81-130">`wwwroot/appsettings.json`：</span><span class="sxs-lookup"><span data-stu-id="83b81-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="83b81-131">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="83b81-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="83b81-132">主机生成器配置</span><span class="sxs-lookup"><span data-stu-id="83b81-132">Host builder configuration</span></span>

<span data-ttu-id="83b81-133">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="83b81-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="83b81-134">缓存的配置</span><span class="sxs-lookup"><span data-stu-id="83b81-134">Cached configuration</span></span>

<span data-ttu-id="83b81-135">配置文件会缓存以供脱机使用。</span><span class="sxs-lookup"><span data-stu-id="83b81-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="83b81-136">使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。</span><span class="sxs-lookup"><span data-stu-id="83b81-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="83b81-137">在部署之间编辑配置文件不起作用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="83b81-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="83b81-138">用户已拥有继续使用的文件的缓存版本。</span><span class="sxs-lookup"><span data-stu-id="83b81-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="83b81-139">PWA 的 `service-worker.js` 和 `service-worker-assets.js` 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署。</span><span class="sxs-lookup"><span data-stu-id="83b81-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="83b81-140">有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。</span><span class="sxs-lookup"><span data-stu-id="83b81-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
