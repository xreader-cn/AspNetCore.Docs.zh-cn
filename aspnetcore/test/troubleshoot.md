---
title: ASP.NET Core 项目疑难解答和调试
author: Rick-Anderson
description: 理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 73a73fb51571e5f7b706ff4b958217854750c1fb
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354710"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core 项目疑难解答和调试

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

以下链接提供了故障排除指南：

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [ASP.NET Core 应用程序中 （ndc） 会议 （伦敦，2018年）： 诊断问题](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET 博客： 解决 ASP.NET Core 性能问题](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK 警告

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>同时安装了 .NET Core SDK 的32位和64位版本

在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：

> 同时安装了 .NET Core SDK 的32位和64位版本。 仅显示64位版本中安装了 "C：\\Program Files\\dotnet\\sdk\\" 的模板。

时，此警告会出现 (x86) 32 位和 64 位 (x64) 版本的[.NET Core SDK](https://www.microsoft.com/net/download/all)安装。 这两个版本可能安装的常见原因包括：

* 你最初下载.NET Core SDK 安装程序使用 32 位计算机，但然后复制它跨并安装在 64 位计算机上。
* 由另一个应用程序安装了 32 位.NET Core SDK。
* 下载并安装了错误的版本。

卸载 32 位.NET Core SDK，以防止出现此警告。 从 **"控制面板" > "** **程序和功能**" 中卸载 > **卸载或更改程序**。 如果了解警告出现的原因及其含义，可以忽略此警告。

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK 的安装在多个位置

在**新项目**对话框为 ASP.NET Core，你可能会看到以下警告：

> .NET Core SDK 安装在多个位置中。 仅显示在 "C：\\Program Files\\dotnet\\sdk\\" 上安装的 Sdk 中的模板。

外部的一个目录中有至少一个安装的.NET Core SDK 时，将显示此消息*c:\\Program Files\\dotnet\\sdk\\* 。 这通常发生在使用复制/粘贴，而不 MSI 安装程序的计算机上部署了.NET Core SDK 时。

卸载所有32位 .NET Core Sdk 和运行时，以防出现此警告。 从 **"控制面板" > "** **程序和功能**" 中卸载 > **卸载或更改程序**。 如果了解警告出现的原因及其含义，可以忽略此警告。

### <a name="no-net-core-sdks-were-detected"></a>检测到没有.NET Core Sdk

* 在 ASP.NET Core 的 Visual Studio "**新建项目**" 对话框中，你可能会看到以下警告：

  > 未检测到任何 .NET Core Sdk，请确保它们包含在环境变量 `PATH`中。

* 执行 `dotnet` 命令时，警告显示为：

  > 找不到任何已安装的 dotnet Sdk。

如果环境变量 `PATH` 不指向计算机上的任何 .NET Core Sdk，则会出现这些警告。 ИфЅвѕцґЛОКМв，ЗлЦґРРТФПВІЩЧч：

* 安装 .NET Core SDK。 从[.Net 下载](https://dotnet.microsoft.com/download)获取最新的安装程序。
* 验证 `PATH` 环境变量是否指向安装 SDK 的位置（对于64位/x64，则为`C:\Program Files\dotnet\`，对于32位/x86 为 `C:\Program Files (x86)\dotnet\`）。 SDK 安装程序通常会设置 `PATH`。 始终在同一台计算机上安装相同的位数 Sdk 和运行时。

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>安装 .NET Core 托管捆绑包后缺少 SDK

安装 .net [Core 托管捆绑](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)会在安装 .net core 运行时以指向 .net core 的32位（x86）版本（`C:\Program Files (x86)\dotnet\`）时修改 `PATH`。 当使用32位（x86） .NET Core `dotnet` 命令时，这可能会导致缺少 Sdk （[未检测到 .Net Core sdk](#no-net-core-sdks-were-detected)）。 若要解决此问题，请将 `C:\Program Files\dotnet\` 移到 `PATH`之前 `C:\Program Files (x86)\dotnet\` 的位置。

## <a name="obtain-data-from-an-app"></a>从应用中获取数据

如果某个应用能够响应请求，则可以使用中间件从应用获取以下数据：

* Request &ndash; 方法，方案，主机，pathbase，路径，查询字符串，标头
* 连接 &ndash; 远程 IP 地址，远程端口，本地 IP 地址，本地端口，客户端证书
* 标识 &ndash; 名称，显示名称
* 配置设置
* 环境变量

将以下[中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)代码置于 `Startup.Configure` 方法的请求处理管道的开头。 在运行中间件之前会检查环境，以确保仅在开发环境中执行代码。

若要获取环境，请使用以下方法之一：

* 将 `IHostingEnvironment` 插入到 `Startup.Configure` 方法中，并检查带有本地变量的环境。 下面的示例代码演示了这种方法。

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

以下链接提供有关 ASP.NET Core 应用程序进行调试的信息。

* [在 Linux 上调试 ASP Core](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [通过 SSH 调试 Unix 上的 .NET Core](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [快速入门：通过 Visual Studio 调试器调试 ASP.NET](/visualstudio/debugger/quickstart-debug-aspnet)
* 有关更多调试信息，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/2960)。
