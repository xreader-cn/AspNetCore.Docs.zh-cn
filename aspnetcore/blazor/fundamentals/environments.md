---
title: ASP.NET Core Blazor 环境
author: guardrex
description: 了解 Blazor 中的环境，包括如何设置 Blazor WebAssembly 应用的环境。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/environments
ms.openlocfilehash: a527e04cf97dd2d2b88dcc6e866475835498545d
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243611"
---
# <a name="aspnet-core-blazor-environments"></a>ASP.NET Core Blazor 环境

> [!NOTE]
> 本主题适用于 Blazor WebAssembly。 若要获取 ASP.NET Core 应用配置的通用指南，请参阅 <xref:fundamentals/environments>。

在本地运行应用时，环境默认为开发环境。 发布应用时，环境默认为生产环境。

托管的 Blazor WebAssembly 应用会通过中间件从服务器中提取环境，该中间件通过添加 `blazor-environment` 标头将该环境传达给浏览器。 标头的值就是环境。 托管的 Blazor 应用和服务器应用共享同一个环境。 有关详细信息（包括如何配置环境），请参阅 <xref:fundamentals/environments>。

对于在本地运行的独立应用，开发服务器会添加 `blazor-environment` 标头来指定开发环境。 要为其他宿主环境指定环境，请添加 `blazor-environment` 标头。

在下面的 IIS 示例中，将自定义标头添加到已发布的 `web.config` 文件中。 `web.config` 文件位于 `bin/Release/{TARGET FRAMEWORK}/publish` 文件夹中：

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
> 要对 IIS 使用在将应用发布到 `publish` 文件夹时不会被覆盖的自定义 `web.config` 文件，请参阅 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>。

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

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/environments>
