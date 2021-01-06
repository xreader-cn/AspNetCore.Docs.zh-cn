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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485962"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="8f108-103">ASP.NET Core Blazor 配置</span><span class="sxs-lookup"><span data-stu-id="8f108-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="8f108-104">本主题适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="8f108-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="8f108-105">若要获取 ASP.NET Core 应用配置的通用指南，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="8f108-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="8f108-106">默认情况下，Blazor WebAssembly 会从以下应用设置文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="8f108-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="8f108-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="8f108-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="8f108-108">`wwwroot/appsettings.{ENVIRONMENT}.json`，其中 `{ENVIRONMENT}` 占位符是应用的[运行时环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="8f108-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="8f108-109">应用注册的其他配置提供程序也可提供配置，但并非所有提供程序或提供程序功能都适用于 Blazor WebAssembly 应用：</span><span class="sxs-lookup"><span data-stu-id="8f108-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="8f108-110">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)：具有客户端密码方案的托管标识和应用程序 ID（客户端 ID）不支持该提供程序。</span><span class="sxs-lookup"><span data-stu-id="8f108-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="8f108-111">不建议将具有客户端密码的应用程序 ID 用于任何 ASP.NET Core 应用（尤其是 Blazor WebAssembly 应用），因为无法在客户端保护客户端密码来访问 Azure Key Vault 服务。</span><span class="sxs-lookup"><span data-stu-id="8f108-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="8f108-112">[Azure 应用配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)：该提供程序不适用于 Blazor WebAssembly 应用，因为 Blazor WebAssembly 应用不会在 Azure 中的服务器上运行。</span><span class="sxs-lookup"><span data-stu-id="8f108-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="8f108-113">Blazor WebAssembly 应用中的配置对用户可见。</span><span class="sxs-lookup"><span data-stu-id="8f108-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="8f108-114">**请勿在 Blazor WebAssembly 应用的配置中存储应用机密、凭据或任何其他敏感数据。**</span><span class="sxs-lookup"><span data-stu-id="8f108-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="8f108-115">有关配置提供程序的详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="8f108-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="8f108-116">应用设置配置</span><span class="sxs-lookup"><span data-stu-id="8f108-116">App settings configuration</span></span>

<span data-ttu-id="8f108-117">会默认加载应用设置文件中的配置。</span><span class="sxs-lookup"><span data-stu-id="8f108-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="8f108-118">在下述示例中，UI 配置值存储在应用设置文件中，由 Blazor 框架自动加载。</span><span class="sxs-lookup"><span data-stu-id="8f108-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="8f108-119">该值由组件读取。</span><span class="sxs-lookup"><span data-stu-id="8f108-119">The value is read by a component.</span></span>

<span data-ttu-id="8f108-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="8f108-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="8f108-121">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入到组件中来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="8f108-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="8f108-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="8f108-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="8f108-123">若要将 `wwwroot` 文件夹中的其他配置文件读入配置，请使用 <xref:System.Net.Http.HttpClient> 获取文件内容。</span><span class="sxs-lookup"><span data-stu-id="8f108-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="8f108-124">以下示例会将配置文件 (`cars.json`) 读取到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="8f108-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="8f108-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="8f108-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="8f108-126">向 `Program.cs` 添加 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 的命名空间：</span><span class="sxs-lookup"><span data-stu-id="8f108-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8f108-127">在 `Program.cs` 的 `Program.Main` 中，修改现有 <xref:System.Net.Http.HttpClient> 注册，以使用客户端读取文件：</span><span class="sxs-lookup"><span data-stu-id="8f108-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

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

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="8f108-128">使用 EF Core 的自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8f108-128">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="8f108-129">使用 <xref:fundamentals/configuration/index#custom-configuration-provider> 中演示的 EF Core 的自定义配置提供程序适用于 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="8f108-129">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

> [!WARNING]
> <span data-ttu-id="8f108-130">使用 Blazor WebAssembly 应用加载的数据库连接字符串和数据库不安全，不得用于存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="8f108-130">Database connection strings and databases loaded with Blazor WebAssembly apps aren't secure and shouldn't be used to store sensitive data.</span></span>

<span data-ttu-id="8f108-131">向应用的项目文件添加 [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) 和 [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="8f108-131">Add package references for [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) and [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) to the app's project file.</span></span>

<span data-ttu-id="8f108-132">添加 <xref:fundamentals/configuration/index#custom-configuration-provider>中描述的 EF Core 配置类。</span><span class="sxs-lookup"><span data-stu-id="8f108-132">Add the EF Core configuration classes described in <xref:fundamentals/configuration/index#custom-configuration-provider>.</span></span>

<span data-ttu-id="8f108-133">将 <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> 和 <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> 的命名空间添加到 `Program.cs`：</span><span class="sxs-lookup"><span data-stu-id="8f108-133">Add namespaces for <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> and <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="8f108-134">在 `Program.cs` 的 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="8f108-134">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="8f108-135">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入到组件中来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="8f108-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="8f108-136">`Pages/EFCoreConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="8f108-136">`Pages/EFCoreConfig.razor`:</span></span>

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

## <a name="memory-configuration-source"></a><span data-ttu-id="8f108-137">内存配置源</span><span class="sxs-lookup"><span data-stu-id="8f108-137">Memory Configuration Source</span></span>

<span data-ttu-id="8f108-138">以下示例使用 `Program.Main` 中的 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> 来提供其他配置。</span><span class="sxs-lookup"><span data-stu-id="8f108-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="8f108-139">向 `Program.cs` 添加 <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> 的命名空间：</span><span class="sxs-lookup"><span data-stu-id="8f108-139">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="8f108-140">在 `Program.cs` 的 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="8f108-140">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="8f108-141">将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 实例注入到组件中来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="8f108-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="8f108-142">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="8f108-142">`Pages/MemoryConfig.razor`:</span></span>

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

<span data-ttu-id="8f108-143">使用 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> 在 C# 代码中获取配置的一部分。</span><span class="sxs-lookup"><span data-stu-id="8f108-143">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8f108-144">以下示例会获取上一示例中的配置的 `wheels` 部分：</span><span class="sxs-lookup"><span data-stu-id="8f108-144">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="8f108-145">身份验证配置</span><span class="sxs-lookup"><span data-stu-id="8f108-145">Authentication configuration</span></span>

<span data-ttu-id="8f108-146">在应用设置文件中提供身份验证配置。</span><span class="sxs-lookup"><span data-stu-id="8f108-146">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="8f108-147">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="8f108-147">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="8f108-148">使用 `Program.Main` 中的 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> 来加载 Identity 提供程序的配置。</span><span class="sxs-lookup"><span data-stu-id="8f108-148">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="8f108-149">以下示例会加载 [OIDC 提供程序](xref:blazor/security/webassembly/standalone-with-authentication-library)的配置。</span><span class="sxs-lookup"><span data-stu-id="8f108-149">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="8f108-150">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="8f108-150">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="8f108-151">日志记录配置</span><span class="sxs-lookup"><span data-stu-id="8f108-151">Logging configuration</span></span>

<span data-ttu-id="8f108-152">向应用的项目文件添加 [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="8f108-152">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="8f108-153">在应用设置文件中，提供日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="8f108-153">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="8f108-154">日志记录配置在 `Program.Main`中加载。</span><span class="sxs-lookup"><span data-stu-id="8f108-154">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="8f108-155">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="8f108-155">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="8f108-156">向 `Program.cs` 添加 <xref:Microsoft.Extensions.Logging?displayProperty=fullName> 的命名空间：</span><span class="sxs-lookup"><span data-stu-id="8f108-156">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="8f108-157">在 `Program.cs` 的 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="8f108-157">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="8f108-158">主机生成器配置</span><span class="sxs-lookup"><span data-stu-id="8f108-158">Host builder configuration</span></span>

<span data-ttu-id="8f108-159">从 `Program.Main` 中的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> 读取主机生成器配置。</span><span class="sxs-lookup"><span data-stu-id="8f108-159">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="8f108-160">在 `Program.cs` 的 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="8f108-160">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="8f108-161">缓存的配置</span><span class="sxs-lookup"><span data-stu-id="8f108-161">Cached configuration</span></span>

<span data-ttu-id="8f108-162">配置文件会缓存以供脱机使用。</span><span class="sxs-lookup"><span data-stu-id="8f108-162">Configuration files are cached for offline use.</span></span> <span data-ttu-id="8f108-163">使用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 时，只能在创建新部署时更新配置文件。</span><span class="sxs-lookup"><span data-stu-id="8f108-163">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="8f108-164">在部署之间编辑配置文件不起作用，原因如下：</span><span class="sxs-lookup"><span data-stu-id="8f108-164">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="8f108-165">用户已拥有继续使用的文件的缓存版本。</span><span class="sxs-lookup"><span data-stu-id="8f108-165">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="8f108-166">PWA 的 `service-worker.js` 和 `service-worker-assets.js` 文件必须在编译时重新生成，这会在用户下一次联机访问时通知应用，指示应用已重新部署。</span><span class="sxs-lookup"><span data-stu-id="8f108-166">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="8f108-167">有关 PWA 如何处理后台更新的详细信息，请参阅 <xref:blazor/progressive-web-app#background-updates>。</span><span class="sxs-lookup"><span data-stu-id="8f108-167">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
