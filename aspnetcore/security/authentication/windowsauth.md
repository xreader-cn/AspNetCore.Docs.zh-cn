---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: scottaddie
description: 了解如何在 ASP.NET Core for IIS 和 HTTP.sys 中配置 Windows 身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330674"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>在 ASP.NET Core 中配置 Windows 身份验证

作者：[Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Windows 身份验证（也称为 "协商"、"Kerberos" 或 "NTLM 身份验证"）可为用[IIS](xref:host-and-deploy/iis/index)、 [Kestrel](xref:fundamentals/servers/kestrel)或[HTTP.sys](xref:fundamentals/servers/httpsys)托管的 ASP.NET Core 应用程序进行配置。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Windows 身份验证（也称为 "协商"、"Kerberos" 或 "NTLM 身份验证"）可为通过[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)承载 ASP.NET Core 应用进行配置。

::: moniker-end

Windows 身份验证依赖于操作系统对 ASP.NET Core 应用程序的用户进行身份验证。 当你的服务器使用 Active Directory 域标识或 Windows 帐户来标识用户时，你可以使用 Windows 身份验证。 Windows 身份验证最适合用于用户、客户端应用和 web 服务器属于同一 Windows 域的 intranet 环境。

> [!NOTE]
> HTTP/2 不支持 Windows 身份验证。 身份验证质询可通过 HTTP/2 响应发送，但客户端必须在进行身份验证之前降级到 HTTP/1.1。

## <a name="proxy-and-load-balancer-scenarios"></a>代理和负载均衡器方案

Windows 身份验证是主要在 intranet 中使用的有状态方案，在此方案中，代理或负载均衡器通常不处理客户端和服务器之间的流量。 如果使用代理或负载平衡器，Windows 身份验证仅适用于代理或负载平衡器：

* 处理身份验证。
* 将用户身份验证信息传递给应用程序（例如，在请求标头中），该信息对身份验证信息起作用。

在使用代理和负载平衡器的环境中，Windows 身份验证的一种替代方法是使用 OpenID Connect （OIDC） Active Directory 联合服务（ADFS）。

## <a name="iisiis-express"></a>IIS/IIS Express

通过 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> 在中调用（命名空间）来添加身份验证服务 `Startup.ConfigureServices` ：

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>启动设置（调试器）

启动设置的配置仅影响 IIS Express 文件*上的属性/launchSettings.js* ，而不会将 IIS 配置为 Windows 身份验证。 [IIS](#iis)部分介绍了服务器配置。

可通过 Visual Studio 或 .NET Core CLI 配置的**Web 应用程序**模板可配置为支持 Windows 身份验证，这将自动更新文件的*属性/launchSettings.js* 。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**新项目**

1. 创建新项目。
1. 选择“ASP.NET Core Web 应用程序”。 选择“下一步”。
1. 在 "**项目名称**" 字段中提供名称。 确认“位置”条目正确无误或为项目提供位置。 选择“创建”  。
1. 选择 "**身份验证**" 下的 "**更改**"。
1. 在 "**更改身份验证**" 窗口中，选择 " **Windows 身份验证**"。 选择“确定”  。
1. 选择“Web 应用程序”。
1. 选择“创建”  。

运行应用。 用户名将显示在呈现的应用的用户界面中。

**现有项目**

项目的属性启用 Windows 身份验证并禁用匿名身份验证：

1. 右键单击“解决方案资源管理器”中的项目，再选择“属性” 。
1. 选择“调试”选项卡。
1. 清除 "**启用匿名身份验证**" 复选框。
1. 选中 "**启用 Windows 身份验证**" 复选框。
1. 保存并关闭属性页。

或者，可以在文件的launchSettings.js的节点中配置属性 `iisSettings` ： *launchSettings.json*

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**新项目**

用[dotnet new](/dotnet/core/tools/dotnet-new) `webapp` 参数（ASP.NET Core Web 应用）和开关执行 dotnet new 命令 `--auth Windows` ：

```dotnetcli
dotnet new webapp --auth Windows
```

**现有项目**

更新 `iisSettings` 文件*上launchSettings.js*的节点：

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

修改现有项目时，确认项目文件包括[AspNetCore 元包](xref:fundamentals/metapackage-app)的包引用**或** [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 包的包引用。

### <a name="iis"></a>IIS

IIS 使用[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)来托管 ASP.NET Core 应用程序。 Windows 身份验证是通过*web.config*文件配置的。 以下部分介绍如何执行这些操作：

* 提供一个本地*web.config*文件，用于在部署应用时在服务器上激活 Windows 身份验证。
* 使用 IIS 管理器配置已部署到服务器的 ASP.NET Core 应用的*web.config*文件。

如果尚未执行此操作，请启用 IIS 以托管 ASP.NET Core 应用。 有关详细信息，请参阅 <xref:host-and-deploy/iis/index>。

为 Windows 身份验证启用 IIS 角色服务。 有关详细信息，请参阅[在 IIS 角色服务中启用 Windows 身份验证（请参阅步骤2）](xref:host-and-deploy/iis/index#iis-configuration)。

默认情况下，将[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)配置为自动对请求进行身份验证。 有关详细信息，请参阅[在 Windows 上利用 iis 选项主机 ASP.NET Core： iis 选项（AutomaticAuthentication）](xref:host-and-deploy/iis/index#iis-options)。

默认情况下，ASP.NET Core 模块配置为将 Windows 身份验证令牌转发到应用程序。 有关详细信息，请参阅[ASP.NET Core 模块配置参考： aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。

使用**以下方法之一：**

* 在**发布和部署项目之前，请**将以下*web.config*文件添加到项目根目录：

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  当项目由 .NET Core SDK 发布（ `<IsTransformWebConfigDisabled>` 在项目文件中没有属性设置为 `true` ）时，已发布的*web.config*文件包含 `<location><system.webServer><security><authentication>` 节。 有关属性的详细信息 `<IsTransformWebConfigDisabled>` ，请参阅 <xref:host-and-deploy/iis/index#webconfig-file> 。

* **发布和部署项目后，请**在 IIS 管理器中执行服务器端配置：

  1. 在 IIS 管理器中，选择 "**连接**" 边栏的 "**站点**" 节点下的 IIS 站点。
  1. 双击**IIS**区域中的 "**身份验证**"。
  1. 选择 "**匿名身份验证**"。 选择 "**操作**" 侧栏中的 "**禁用**"。
  1. 选择 **“Windows 身份验证”**。 选择 "**操作**" 侧栏中的 "**启用**"。

  执行这些操作时，IIS 管理器会修改应用的*web.config*文件。 `<system.webServer><security><authentication>`添加了和的更新设置的节点 `anonymousAuthentication` `windowsAuthentication` ：

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`IIS 管理器添加到*web.config*文件的部分不在 `<location>` 应用发布时 .NET Core SDK 添加的应用部分。 由于将节添加到了节点外部 `<location>` ，因此任何[子应用](xref:host-and-deploy/iis/index#sub-applications)都将这些设置继承到当前应用。 若要防止继承，请将添加的部分移动到 `<security>` `<location><system.webServer>` .NET Core SDK 提供的部分中。

  当使用 IIS 管理器添加 IIS 配置时，它只会影响该应用程序在服务器上的*web.config*文件。 如果*web.config*的服务器副本替换为项目的*web.config*文件，则该应用的后续部署可能会覆盖服务器上的设置。 使用以下**两**种方法之一来管理设置：

  * 在部署时覆盖文件后，使用 IIS 管理器重置*web.config*文件中的设置。
  * 使用设置在本地将*web.config 文件*添加到应用。

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 包可与[Kestrel](xref:fundamentals/servers/kestrel)配合使用，以支持在 Windows、Linux 和 macOS 上使用 Negotiate 和 Kerberos 的 windows 身份验证。

> [!WARNING]
> 可以在连接的请求间保留凭据。 *除非代理维护1:1 连接关联（持久性连接）与 Kestrel，否则协商身份验证不能与代理一起使用。*

> [!NOTE]
> Negotiate 处理程序检测基础服务器是否支持本机 Windows 身份验证以及是否已启用。 如果服务器支持 Windows 身份验证，但禁用了它，则会引发错误，要求你启用服务器实现。 如果在服务器中启用了 Windows 身份验证，则协商处理程序会以透明方式转发到该身份验证。

通过调用和中的来添加身份验证服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` ：

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

通过在中调用来添加身份验证中间件 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` ：

 ```csharp
app.UseAuthentication();
```

有关中间件的详细信息，请参阅 <xref:fundamentals/middleware/index> 。

允许匿名请求。 使用[ASP.NET Core 授权](xref:security/authorization/introduction)质询请求身份验证的匿名请求。

### <a name="windows-environment-configuration"></a>Windows 环境配置

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)组件执行用户模式身份验证。 必须将服务主体名称（Spn）添加到运行服务的用户帐户，而不是计算机帐户。 `setspn -S HTTP/myservername.mydomain.com myuser`在管理命令行界面中执行。

### <a name="linux-and-macos-environment-configuration"></a>Linux 和 macOS 环境配置

有关将 Linux 或 macOS 计算机加入到 Windows 域的说明，请访问[使用 windows 身份验证将 Azure Data Studio 连接到 SQL Server-Kerberos 一](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)文。 说明为域中的 Linux 计算机创建计算机帐户。 必须将 Spn 添加到该计算机帐户。

> [!NOTE]
> 按照[使用 Windows 身份验证将 Azure Data Studio 连接到 SQL Server](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)中的指导进行时，请在需要时将替换 `python-software-properties` 为 `python3-software-properties` 。

将 Linux 或 macOS 计算机加入到域后，还需要执行其他步骤以使用 Spn 提供[keytab 文件](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)：

* 在域控制器上，向计算机帐户添加新的 web 服务 Spn：
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* 使用[ktpass](/windows-server/administration/windows-commands/ktpass)生成 keytab 文件：
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * 某些字段必须以大写字母指定，如所示。
* 将 keytab 文件复制到 Linux 或 macOS 计算机。
* 通过环境变量选择 keytab 文件：`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* 调用 `klist` 以显示当前可供使用的 spn。

> [!NOTE]
> Keytab 文件包含域访问凭据并且必须相应地受到保护。

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys)支持使用协商、NTLM 或基本身份验证的内核模式 Windows 身份验证。

通过 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 在中调用（命名空间）来添加身份验证服务 `Startup.ConfigureServices` ：

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

将应用程序的 web 主机配置为使用 HTTP.sys Windows 身份验证（*Program.cs*）。 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>位于 <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 命名空间中。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。 Kerberos 和 HTTP.sys 不支持用户模式身份验证。 必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。 注册主机的服务主体名称 (SPN)，而不是应用的用户。

> [!NOTE]
> Nano Server 版本1709或更高版本不支持 HTTP.sys。 若要将 Windows 身份验证和 HTTP.sys 与 Nano Server 一起使用，请使用[服务器核心（microsoft/windowsservercore）容器](https://hub.docker.com/r/microsoft/windowsservercore/)。 有关服务器核心的详细信息，请参阅[Windows server 中的什么是服务器核心安装选项？](/windows-server/administration/server-core/what-is-server-core)。

## <a name="authorize-users"></a>对用户授权

匿名访问的配置状态决定了 `[Authorize]` 和 `[AllowAnonymous]` 属性在应用中的使用方式。 以下两节说明如何处理匿名访问的禁止和允许的配置状态。

### <a name="disallow-anonymous-access"></a>禁止匿名访问

启用 Windows 身份验证并禁用匿名访问时， `[Authorize]` 和 `[AllowAnonymous]` 属性不起作用。 如果将 IIS 站点配置为不允许匿名访问，则请求将永远不会到达该应用。 出于此原因，该 `[AllowAnonymous]` 属性不适用。

### <a name="allow-anonymous-access"></a>允许匿名访问

同时启用 Windows 身份验证和匿名访问时，请使用 `[Authorize]` 和 `[AllowAnonymous]` 属性。 `[Authorize]`属性允许保护需要身份验证的应用的终结点。 `[AllowAnonymous]`特性替代 `[Authorize]` 允许匿名访问的应用程序中的特性。 有关属性用法的详细信息，请参阅 <xref:security/authorization/simple> 。

> [!NOTE]
> 默认情况下，如果用户无权访问页面，则会显示空的 HTTP 403 响应。 可以将[StatusCodePages 中间件](xref:fundamentals/error-handling#usestatuscodepages)配置为向用户提供更好的 "访问被拒绝" 体验。

## <a name="impersonation"></a>模拟

ASP.NET Core 不实现模拟。 应用使用应用池或进程标识，使用应用的标识运行所有请求。 如果应用应代表用户执行操作，请在中的[终端内联中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)中使用[RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 。 `Startup.Configure` 在此上下文中运行单个操作，然后关闭上下文。

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`不支持异步操作，因此不应用于复杂方案。 例如，不支持包装整个请求或中间件链，也不建议使用。

::: moniker range=">= aspnetcore-3.0"

尽管[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)包在 Windows、Linux 和 macOS 上启用身份验证，但仅在 windows 上支持模拟。

::: moniker-end

## <a name="claims-transformations"></a>声明转换

::: moniker range=">= aspnetcore-3.0"

当使用 IIS 进行托管时， <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 不会在内部调用来初始化用户。 因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。 有关激活声明转换的详细信息和代码示例，请参阅 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

当使用 IIS 进程内模式进行承载时， <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 不会在内部调用来初始化用户。 因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。 有关在进程内承载时激活声明转换的详细信息和代码示例，请参阅 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
