---
title: ASP.NET Core Blazor 配置
author: guardrex
description: 了解 Blazor 应用的配置，包括应用设置、身份验证和日志记录配置。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485962"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>ASP.NET Core Blazor 配置

> [!NOTE]
> 本主题适用于 Blazor WebAssembly。 若要获取 ASP.NET Core 应用配置的通用指南，请参阅 <xref:fundamentals/configuration/index>。

默认情况下，Blazor WebAssembly 会从以下应用设置文件加载配置：

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`，其中 `{ENVIRONMENT}` 占位符是应用的[运行时环境](xref:fundamentals/environments)。

应用注册的其他配置提供程序也可提供配置，但并非所有提供程序或提供程序功能都适用于 Blazor WebAssembly 应用：

* [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)：具有客户端密码方案的托管标识和应用程序 ID（客户端 ID）不支持该提供程序。 不建议将具有客户端密码的应用程序 ID 用于任何 ASP.NET Core 应用（尤其是 Blazor WebAssembly 应用），因为无法在客户端保护客户端密码来访问 Azure Key Vault 服务。
* [Azure 应用配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)：该提供程序不适用于 Blazor WebAssembly 应用，因为 Blazor WebAssembly 应用不会在 Azure 中的服务器上运行。

> [!WARNING]
> Blazor WebAssembly 应用中的配置对用户可见。 **请勿在 Blazor WebAssembly 应用的配置中存储应用机密、凭据或任何其他敏感数据。**

有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。

## <a name="app-settings-configuration"></a>应用设置配置

会默认加载应用设置文件中的配置。 在下述示例中，UI 配置值存储在应用设置文件中，由 Blazor 框架自动加载。 该值由组件读取。

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入到组件中来访问配置数据。

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

若要将 `wwwroot` 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。 以下示例会将配置文件 (`cars.json`) 读取到应用的配置中。

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

向 `Program.cs` 添加 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 的命名空间：

```csharp
using Microsoft.Extensions.Configuration;
```

在 `Program.cs` 的 `Program.Main` 中，修改现有 <xref:System.Net.Http.HttpClient> 注册，以使用客户端读取文件：

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="custom-configuration-provider-with-ef-core"></a>使用 EF Core 的自定义配置提供程序

使用 <xref:fundamentals/configuration/index#custom-configuration-provider> 中演示的 EF Core 的自定义配置提供程序适用于 Blazor WebAssembly 应用。

> [!WARNING]
> 使用 Blazor WebAssembly 应用加载的数据库连接字符串和数据库不安全，不得用于存储敏感数据。

向应用的项目文件添加 [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) 和 [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) 的包引用。

添加 <xref:fundamentals/configuration/index#custom-configuration-provider>中描述的 EF Core 配置类。

将 <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> 和 <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> 的命名空间添加到 `Program.cs`：

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

在 `Program.cs` 的 `Program.Main` 中：

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入到组件中来访问配置数据。

`Pages/EFCoreConfig.razor`:

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a>内存配置源

以下示例使用 `Program.Main` 中的 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 来提供其他配置。

向 `Program.cs` 添加 <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> 的命名空间：

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

在 `Program.cs` 的 `Program.Main` 中：

```csharp
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

builder.Configuration.Add(memoryConfig);
```

将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入到组件中来访问配置数据。

`Pages/MemoryConfig.razor`:

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

使用 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> 在 C# 代码中获取配置的一部分。 以下示例会获取上一示例中的配置的 `wheels` 部分：

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>身份验证配置

在应用设置文件中提供身份验证配置。

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

使用 `Program.Main` 中的 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> 来加载 Identity 提供程序的配置。 以下示例会加载 [OIDC 提供程序](xref:blazor/security/webassembly/standalone-with-authentication-library)的配置。

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>日志记录配置

向应用的项目文件添加 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) 的包引用。 在应用设置文件中，提供日志记录配置。 日志记录配置在 `Program.Main`中加载。

`wwwroot/appsettings.json`:

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

向 `Program.cs` 添加 <xref:Microsoft.Extensions.Logging?displayProperty=fullName> 的命名空间：

```csharp
using Microsoft.Extensions.Logging;
```

在 `Program.cs` 的 `Program.Main` 中：

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>主机生成器配置

从 `Program.Main` 中的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> 读取主机生成器配置。

在 `Program.cs` 的 `Program.Main` 中：

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>缓存的配置

配置文件会缓存以供脱机使用。 使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。 在部署之间编辑配置文件不起作用，原因如下：

* 用户已拥有继续使用的文件的缓存版本。
* PWA 的 `service-worker.js` 和 `service-worker-assets.js` 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署。

有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。
