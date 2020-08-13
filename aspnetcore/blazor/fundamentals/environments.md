---
title: ASP.NET Core Blazor 环境
author: guardrex
description: 了解 Blazor 中的环境，包括如何设置 Blazor WebAssembly 应用的环境。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/environments
ms.openlocfilehash: 8a672b3d2aff4dd2b80465b0f6dac038d299eaa9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014420"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="14408-103">ASP.NET Core Blazor 环境</span><span class="sxs-lookup"><span data-stu-id="14408-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="14408-104">本主题适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="14408-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="14408-105">若要获取 ASP.NET Core 应用配置的通用指南，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="14408-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="14408-106">在本地运行应用时，环境默认为开发环境。</span><span class="sxs-lookup"><span data-stu-id="14408-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="14408-107">发布应用时，环境默认为生产环境。</span><span class="sxs-lookup"><span data-stu-id="14408-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="14408-108">托管的 Blazor WebAssembly 应用会通过中间件从服务器中提取环境，该中间件通过添加 `blazor-environment` 标头将该环境传达给浏览器。</span><span class="sxs-lookup"><span data-stu-id="14408-108">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="14408-109">标头的值就是环境。</span><span class="sxs-lookup"><span data-stu-id="14408-109">The value of the header is the environment.</span></span> <span data-ttu-id="14408-110">托管的 Blazor 应用和服务器应用共享同一个环境。</span><span class="sxs-lookup"><span data-stu-id="14408-110">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="14408-111">有关详细信息（包括如何配置环境），请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="14408-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="14408-112">对于在本地运行的独立应用，开发服务器会添加 `blazor-environment` 标头来指定开发环境。</span><span class="sxs-lookup"><span data-stu-id="14408-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="14408-113">要为其他宿主环境指定环境，请添加 `blazor-environment` 标头。</span><span class="sxs-lookup"><span data-stu-id="14408-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="14408-114">在下面的 IIS 示例中，将自定义标头添加到已发布的 `web.config` 文件中。</span><span class="sxs-lookup"><span data-stu-id="14408-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="14408-115">`web.config` 文件位于 `bin/Release/{TARGET FRAMEWORK}/publish` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="14408-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="14408-116">要对 IIS 使用在将应用发布到 `publish` 文件夹时不会被覆盖的自定义 `web.config` 文件，请参阅 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="14408-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="14408-117">通过注入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 并读取 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 属性，在组件中获取应用的环境：</span><span class="sxs-lookup"><span data-stu-id="14408-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="14408-118">在启动过程中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> 会通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 属性公开 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>，这让开发人员能够在其代码中拥有环境特定的逻辑：</span><span class="sxs-lookup"><span data-stu-id="14408-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="14408-119">通过下面的便捷扩展方法，可在当前环境中检查开发环境、生产环境、暂存环境和自定义环境名称：</span><span class="sxs-lookup"><span data-stu-id="14408-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="14408-120">如果 <xref:Microsoft.AspNetCore.Components.NavigationManager> 服务不可用，则启动期间可使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 属性。</span><span class="sxs-lookup"><span data-stu-id="14408-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14408-121">其他资源</span><span class="sxs-lookup"><span data-stu-id="14408-121">Additional resources</span></span>

* <xref:fundamentals/environments>
