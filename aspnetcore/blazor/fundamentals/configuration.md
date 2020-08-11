---
title: ASP.NET Core Blazor 配置
author: guardrex
description: 了解 Blazor 应用的配置，包括应用设置、身份验证和日志记录配置。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 9ae0dcc16b9debd47a61010953243b0abe499c4f
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87443965"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="9eef3-103">ASP.NET Core Blazor 配置</span><span class="sxs-lookup"><span data-stu-id="9eef3-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="9eef3-104">本主题适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="9eef3-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="9eef3-105">若要获取 ASP.NET Core 应用配置的通用指南，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="9eef3-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="9eef3-106">默认情况下，Blazor WebAssembly 从应用设置文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="9eef3-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="9eef3-107">应用注册的其他配置提供程序还可以提供配置。</span><span class="sxs-lookup"><span data-stu-id="9eef3-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="9eef3-108">并非所有提供程序或提供程序功能都适用于 Blazor WebAssembly 应用：</span><span class="sxs-lookup"><span data-stu-id="9eef3-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="9eef3-109">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)：具有客户端密码方案的托管标识和应用程序 ID（客户端 ID）不支持该提供程序。</span><span class="sxs-lookup"><span data-stu-id="9eef3-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="9eef3-110">不建议将具有客户端密码的应用程序 ID 用于任何 ASP.NET Core 应用（尤其是 Blazor WebAssembly 应用），因为无法在客户端保护客户端密码来访问服务。</span><span class="sxs-lookup"><span data-stu-id="9eef3-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="9eef3-111">[Azure 应用配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)：该提供程序不适用于 Blazor WebAssembly 应用，因为 Blazor WebAssembly 应用不会在 Azure 中的服务器上运行。</span><span class="sxs-lookup"><span data-stu-id="9eef3-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="9eef3-112">Blazor WebAssembly 应用中的配置对用户可见。</span><span class="sxs-lookup"><span data-stu-id="9eef3-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="9eef3-113">请勿在配置中存储应用机密或凭据。</span><span class="sxs-lookup"><span data-stu-id="9eef3-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="9eef3-114">有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="9eef3-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="9eef3-115">应用设置配置</span><span class="sxs-lookup"><span data-stu-id="9eef3-115">App settings configuration</span></span>

<span data-ttu-id="9eef3-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9eef3-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="9eef3-117">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="9eef3-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="9eef3-118">使用 EF Core 的自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="9eef3-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="9eef3-119">使用 <xref:fundamentals/configuration/index#custom-configuration-provider> 中演示的 EF Core 的自定义配置提供程序适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="9eef3-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="9eef3-120">在 `Program.Main` (`Program.cs`) 中使用以下代码添加示例的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="9eef3-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="9eef3-121">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="9eef3-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="9eef3-122">内存配置源</span><span class="sxs-lookup"><span data-stu-id="9eef3-122">Memory Configuration Source</span></span>

<span data-ttu-id="9eef3-123">以下示例使用 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 提供其他配置：</span><span class="sxs-lookup"><span data-stu-id="9eef3-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="9eef3-124">`Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="9eef3-124">`Program.Main`:</span></span>

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

<span data-ttu-id="9eef3-125">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入组件，以访问配置数据：</span><span class="sxs-lookup"><span data-stu-id="9eef3-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="9eef3-126">若要将 `wwwroot` 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="9eef3-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="9eef3-127">使用此方法时，现有 <xref:System.Net.Http.HttpClient> 服务注册可以使用创建的本地客户端来读取文件，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="9eef3-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="9eef3-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="9eef3-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="9eef3-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9eef3-129">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="9eef3-130">身份验证配置</span><span class="sxs-lookup"><span data-stu-id="9eef3-130">Authentication configuration</span></span>

<span data-ttu-id="9eef3-131">`wwwroot/appsettings.json`：</span><span class="sxs-lookup"><span data-stu-id="9eef3-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="9eef3-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9eef3-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="9eef3-133">日志记录配置</span><span class="sxs-lookup"><span data-stu-id="9eef3-133">Logging configuration</span></span>

<span data-ttu-id="9eef3-134">添加 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) 的包引用：</span><span class="sxs-lookup"><span data-stu-id="9eef3-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="9eef3-135">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9eef3-135">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="9eef3-136">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9eef3-136">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="9eef3-137">主机生成器配置</span><span class="sxs-lookup"><span data-stu-id="9eef3-137">Host builder configuration</span></span>

<span data-ttu-id="9eef3-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9eef3-138">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="9eef3-139">缓存的配置</span><span class="sxs-lookup"><span data-stu-id="9eef3-139">Cached configuration</span></span>

<span data-ttu-id="9eef3-140">配置文件会缓存以供脱机使用。</span><span class="sxs-lookup"><span data-stu-id="9eef3-140">Configuration files are cached for offline use.</span></span> <span data-ttu-id="9eef3-141">使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。</span><span class="sxs-lookup"><span data-stu-id="9eef3-141">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="9eef3-142">在部署之间编辑配置文件不起作用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="9eef3-142">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="9eef3-143">用户已拥有继续使用的文件的缓存版本。</span><span class="sxs-lookup"><span data-stu-id="9eef3-143">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="9eef3-144">PWA 的 `service-worker.js` 和 `service-worker-assets.js` 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署。</span><span class="sxs-lookup"><span data-stu-id="9eef3-144">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="9eef3-145">有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。</span><span class="sxs-lookup"><span data-stu-id="9eef3-145">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
