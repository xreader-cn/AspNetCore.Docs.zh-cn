---
title: ASP.NET Core 项目故障排除和调试
author: Rick-Anderson
description: 理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059567"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core 项目故障排除和调试

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

以下链接提供了故障排除指南：

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC 会议（伦敦，2018 年）：诊断 ASP.NET Core 应用程序中的问题](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET 博客：解决 ASP.NET Core 性能问题](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK 警告

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>同时安装了 .NET Core SDK 的 32 位和 64 位版本

在 ASP.NET Core 的“新建项目”对话框中，可能会显示以下警告：

> 同时安装了 .NET Core SDK 的 32 位和 64 位版本。 仅显示安装在 C:\\Program Files\\dotnet\\sdk\\ 的 64 位版本的模板。

如果同时安装了 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 的 32 位 (x86) 和 64 位 (x64) 版本，则会出现此警告。 可能同时安装两个版本的常见原因包括：

* 最初使用 32 位计算机下载了 .NET Core SDK 安装程序，然后将其复制并安装到 64 位计算机上。
* 32 位 .NET Core SDK 是由另一个应用程序安装的。
* 下载并安装了错误的版本。

卸载 32 位 .NET Core SDK 以避免出现此警告。 从“控制面板” > “程序和功能” > “卸载或更改程序”卸载  。 如果了解警告出现的原因及其含义，可以忽略此警告。

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK 安装在多个位置

在 ASP.NET Core 的“新建项目”对话框中，可能会显示以下警告：

> .NET Core SDK 安装在多个位置。 仅显示安装在 C:\\Program Files\\dotnet\\sdk\\ 的 SDK 的模板。

如果在 C:\\Program Files\\dotnet\\sdk\\ 之外的目录中至少安装了一个 .NET Core SDK，则会看到此消息。 发生这种情况，通常是由于使用复制/粘贴而不是 MSI 安装程序在计算机上部署了 .NET Core SDK。

卸载所有 32 位 .NET Core SDK 和运行时，以防出现此警告。 从“控制面板” > “程序和功能” > “卸载或更改程序”卸载  。 如果了解警告出现的原因及其含义，可以忽略此警告。

### <a name="no-net-core-sdks-were-detected"></a>未检测到 .NET Core SDK

* 在 Visual Studio 中，ASP.NET Core 的“新建项目”对话框中可能会显示以下警告：

  > 未检测到任何 .NET Core SDK，请确保它们包含在环境变量 `PATH` 中。

* 执行 `dotnet` 命令时，警告显示为：

  > 找不到任何已安装的 dotnet SDK。

如果环境变量 `PATH` 不指向计算机上的任何 .NET Core SDK，则会出现这些警告。 若要解决此问题，请执行以下操作:

* 安装 .NET Core SDK。 从 [.NET 下载](https://dotnet.microsoft.com/download)获取最新的安装程序。
* 验证 `PATH` 环境变量是否指向安装 SDK 的位置（对于 64 位/x64，为 `C:\Program Files\dotnet\`，对于 32 位/x86，为 `C:\Program Files (x86)\dotnet\`）。 SDK 安装程序通常会设置 `PATH`。 始终在同一台计算机上安装相同位数的 SDK 和运行时。

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>安装 .NET Core 托管捆绑包后缺少 SDK

如果安装了 .NET Core 运行时以指向 32 位 (x86) 版本的 .NET Core (`C:\Program Files (x86)\dotnet\`)，那么安装 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)会修改 `PATH`。 当使用 32 位 (x86) .NET Core `dotnet` 命令时，这可能会导致缺少 SDK（[未检测到 .NET Core SDK](#no-net-core-sdks-were-detected)）。 若要解决此问题，请将 `C:\Program Files\dotnet\` 移到 `PATH` 上 `C:\Program Files (x86)\dotnet\` 之前的位置。

## <a name="obtain-data-from-an-app"></a>从应用中获取数据

如果某个应用能够响应请求，则可以使用中间件从应用获取以下数据：

* 请求：方法、方案、主机、基路径、路径、查询字符串、标头
* 连接: 远程 IP 地址、远程端口、本地 IP 地址、本地端口、客户端证书
* Identity：名称、显示名称
* 配置设置
* 环境变量

将以下[中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)代码置于 `Startup.Configure` 方法的请求处理管道的开头。 在运行中间件之前会检查环境，以确保仅在开发环境中执行代码。

若要获取环境，请使用以下方法之一：

* 将 `IHostingEnvironment` 插入到 `Startup.Configure` 方法中，并检查带有本地变量的环境。 下面的示例代码对此方法进行了演示。

* 将环境分配到 `Startup` 类中的属性。 使用属性（例如 `if (Environment.IsDevelopment())`）检查环境。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>调试 ASP.NET Core 应用

以下链接提供有关调试 ASP.NET Core 应用的信息。

* [在 Linux 上调试 ASP Core](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [通过 SSH 在 Unix 上调试 .NET Core](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [快速入门：使用 Visual Studio 调试程序调试 ASP.NET](/visualstudio/debugger/quickstart-debug-aspnet)
* 有关更多调试信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/2960)。
