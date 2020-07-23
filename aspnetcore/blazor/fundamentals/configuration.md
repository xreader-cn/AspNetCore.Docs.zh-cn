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
ms.openlocfilehash: f78803a3954feb98a39f26874b9de0aa08dc6327
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445211"
---
# <a name="aspnet-core-blazor-configuration"></a>ASP.NET Core Blazor 配置

> [!NOTE]
> 本主题适用于 Blazor WebAssembly。 若要获取 ASP.NET Core 应用配置的通用指南，请参阅 <xref:fundamentals/configuration/index>。

Blazor WebAssembly 加载以下来源的配置：

* 应用设置文件（默认）：
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* 应用注册的其他 [配置提供程序](xref:fundamentals/configuration/index)。 并非所有提供程序都适用于 Blazor WebAssembly 应用。 Blazor WASM 的 [Clarify 配置提供程序 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134) 会跟踪有关 Blazor WebAssembly 所支持提供程序的说明。

> [!WARNING]
> Blazor WebAssembly 应用中的配置对用户可见。 请勿在配置中存储应用机密或凭据。

有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。

## <a name="app-settings-configuration"></a>应用设置配置

`wwwroot/appsettings.json`：

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

## <a name="provider-configuration"></a>提供程序配置

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

若要将 `wwwroot` 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。 使用此方法时，现有 <xref:System.Net.Http.HttpClient> 服务注册可以使用创建的本地客户端来读取文件，如以下示例所示：

`wwwroot/cars.json`：

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

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a>身份验证配置

`wwwroot/appsettings.json`：

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
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>日志记录配置

添加 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) 的包引用：

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

`wwwroot/appsettings.json`：

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
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>主机生成器配置

`Program.Main`：

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>缓存的配置

配置文件会缓存以供脱机使用。 使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。 在部署之间编辑配置文件不起作用，原因如下：

* 用户已拥有继续使用的文件的缓存版本。
* PWA 的 `service-worker.js` 和 `service-worker-assets.js` 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署。

有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。
