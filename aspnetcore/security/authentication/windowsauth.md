---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: scottaddie
description: 了解如何为 IIS 和 HTTP.sys 在 ASP.NET Core 中配置 Windows 身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/12/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 93f833adff95f25d570947cd1a9035d652f522c2
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034949"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>在 ASP.NET Core 中配置 Windows 身份验证

通过[Scott Addie](https://twitter.com/Scott_Addie)和[Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Windows 身份验证 （也称为协商、 Kerberos 或 NTLM 的身份验证） 可以配置为使用托管的 ASP.NET Core 应用[IIS](xref:host-and-deploy/iis/index)， [Kestrel](xref:fundamentals/servers/kestrel)，或[HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Windows 身份验证 （也称为协商、 Kerberos 或 NTLM 的身份验证） 可以配置为使用托管的 ASP.NET Core 应用[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)。

::: moniker-end

Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。 使用 Active Directory 域标识或 Windows 帐户标识的用户在公司网络上运行你的服务器时，可以使用 Windows 身份验证。 Windows 身份验证是最适合于用户、 客户端应用程序和 web 服务器属于同一个 Windows 域的 intranet 环境。

> [!NOTE]
> 使用 HTTP/2 不支持 Windows 身份验证。 可以在 HTTP/2 响应发送身份验证质询，但客户端必须降级到 HTTP/1.1 之前进行身份验证。

## <a name="iisiis-express"></a>IIS/IIS Express

添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName>命名空间) 中`Startup.ConfigureServices`:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>启动设置 （调试器）

用于配置启动设置，只会影响*Properties/launchSettings.json* IIS express 文件，并不会配置 IIS 的 Windows 身份验证。 服务器配置中所述[IIS](#iis)部分。

**Web 应用程序**可通过 Visual Studio 或.NET Core CLI 的模板可以配置为支持 Windows 身份验证，这会更新*Properties/launchSettings.json*文件是自动的。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**新项目**

1. 创建新项目。
1. 选择“ASP.NET Core Web 应用程序”  。 选择“下一步”  。
1. 中提供名称**项目名称**字段。 确认**位置**条目是否正确，或提供项目的位置。 选择“创建”  。
1. 选择**更改**下**身份验证**。
1. 在中**更改身份验证**窗口中，选择**Windows 身份验证**。 选择 **确定**。
1. 选择“Web 应用程序”  。
1. 选择“创建”  。

运行应用。 用户名将显示在呈现的应用程序用户界面。

**现有项目**

项目的属性启用 Windows 身份验证并禁用匿名身份验证：

1. 右键单击“解决方案资源管理器”中的项目，再选择“属性”   。
1. 选择“调试”选项卡  。
1. 清除的复选框**启用匿名身份验证**。
1. 选中的复选框**启用 Windows 身份验证**。
1. 保存并关闭属性页。

或者，可以在配置属性`iisSettings`的节点*launchSettings.json*文件：

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

**新项目**

执行[dotnet 新](/dotnet/core/tools/dotnet-new)命令`webapp`自变量 （ASP.NET Core Web 应用） 和`--auth Windows`切换：

```console
dotnet new webapp --auth Windows
```

**现有项目**

更新`iisSettings`的节点*launchSettings.json*文件：

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

在修改现有项目，确认项目文件包含的包引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)**或** [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 包。

### <a name="iis"></a>IIS

IIS 使用[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)到承载 ASP.NET Core 应用程序。 Windows 身份验证配置为通过 IIS *web.config*文件。 以下部分介绍如何：

* 提供本地*web.config*在部署应用时在服务器激活 Windows 身份验证的文件。
* 使用 IIS 管理器配置*web.config*已部署到服务器的 ASP.NET Core 应用的文件。

如果尚未执行此操作，使 IIS 能够承载 ASP.NET Core 应用程序。 有关详细信息，请参阅 <xref:host-and-deploy/iis/index>。

启用 Windows 身份验证的 IIS 角色服务。 有关详细信息，请参阅[IIS 角色服务 （请参阅步骤 2） 中启用 Windows 身份验证](xref:host-and-deploy/iis/index#iis-configuration)。

[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)默认配置为自动进行身份验证请求。 有关详细信息，请参阅[与 IIS 的 Windows 上托管 ASP.NET Core:IIS 选项 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)。

默认情况下，ASP.NET Core 模块配置为转发到应用的 Windows 身份验证令牌。 有关详细信息，请参阅[ASP.NET Core 模块配置参考：AspNetCore 元素的特性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。

使用**任一**以下方法之一：

* **发布和部署该项目之前,** 添加以下*web.config*到项目根目录的文件：

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  由.NET Core SDK 发布项目时 (而无需`<IsTransformWebConfigDisabled>`属性设置为`true`项目文件中)，已发布*web.config*文件包括`<location><system.webServer><security><authentication>`部分。 有关详细信息`<IsTransformWebConfigDisabled>`属性，请参阅<xref:host-and-deploy/iis/index#webconfig-file>。

* **发布和部署该项目之后,** 执行服务器端配置使用 IIS 管理器：

  1. 在 IIS 管理器中，选择下的 IIS 站点**站点**的节点**连接**侧栏。
  1. 双击**身份验证**中**IIS**区域。
  1. 选择**匿名身份验证**。 选择**禁用**中**操作**侧栏。
  1. 选择**Windows 身份验证**。 选择**启用**中**操作**侧栏。

  IIS 管理器时执行这些操作，会应用的修改*web.config*文件。 一个`<system.webServer><security><authentication>`使用更新的设置的添加节点`anonymousAuthentication`和`windowsAuthentication`:

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`部分添加到*web.config*文件由 IIS 管理器是在应用外`<location>`发布应用时由.NET Core SDK 添加的部分。 因为部分添加之外`<location>`节点中，设置由任何继承[子应用](xref:host-and-deploy/iis/index#sub-applications)到当前应用。 若要阻止继承，将在添加`<security>`内的部分`<location><system.webServer>`.NET Core SDK 提供的部分。

  当使用 IIS 管理器添加 IIS 配置时，它只影响应用程序的*web.config*在服务器上的文件。 应用程序的后续部署可能会覆盖服务器上的设置，如果服务器的副本*web.config*替换为项目的*web.config*文件。 使用**任一**以下方法来管理的设置之一：

  * 使用 IIS 管理器中的设置重置*web.config*文件后部署上覆盖该文件。
  * 添加*web.config 文件*向本地使用的设置应用程序。

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 包可用于[Kestrel](xref:fundamentals/servers/kestrel)以支持 Windows、 Linux 和 macOS 上使用 Negotiate、 Kerberos 和 NTLM 的 Windows 身份验证。

> [!WARNING]
> 凭据可以在连接上的请求之间得以保持。 *协商身份验证不得使用代理中使用，除非代理维护与 Kestrel 1 对 1 连接关联 （持续性连接）。* 这意味着，协商身份验证必须不能与使用 Kestrel 在 IIS 后方[ASP.NET Core 模块 (ANCM) 进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)。

 添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(`Microsoft.AspNetCore.Authentication.Negotiate`命名空间) 和`AddNegotitate`(`Microsoft.AspNetCore.Authentication.Negotiate`命名空间) 中`Startup.ConfigureServices`:

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

通过调用添加身份验证中间件<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>在`Startup.Configure`:

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

中间件的详细信息，请参阅<xref:fundamentals/middleware/index>。

允许匿名请求。 使用[ASP.NET Core 授权](xref:security/authorization/introduction)质询的身份验证的匿名请求。

### <a name="windows-environment-configuration"></a>Windows 环境配置

[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)组件执行用户模式身份验证。 服务主体名称 (Spn) 必须添加到运行服务，而不是计算机帐户的用户帐户。 执行`setspn -S HTTP/mysrevername.mydomain.com myuser`管理命令行界面中。

### <a name="linux-and-macos-environment-configuration"></a>Linux 和 macOS 的环境配置

中提供了有关在 Linux 或 macOS 计算机加入 Windows 域的说明[Azure 数据 Studio 连接到 SQL Server 使用 Windows 身份验证的 Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)一文。 说明在域中创建的 Linux 计算机的计算机帐户。 必须将 Spn 添加到该计算机帐户。

> [!NOTE]
> 时中的指南[Azure 数据 Studio 连接到 SQL Server 使用 Windows 身份验证的 Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)文章中，替换`python-software-properties`与`python3-software-properties`必要。

一旦在 Linux 或 macOS 计算机加入到域，需要提供额外的步骤[keytab 文件](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)的 spn:

* 在域控制器上，将添加到的计算机帐户的新的 web 服务的 Spn:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* 使用[ktpass](/windows-server/administration/windows-commands/ktpass)生成 keytab 文件：
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * 某些字段必须以指定大写所示。
* 将 keytab 文件复制到 Linux 或 macOS 计算机。
* 选择通过环境变量的 keytab 文件： `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* 调用`klist`以显示当前可供使用的 Spn。

> [!NOTE]
> Keytab 文件包含域访问凭据，必须相应地保护。

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys)支持内核模式 Windows 身份验证使用 Negotiate、 NTLM 或基本身份验证。

添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>命名空间) 中`Startup.ConfigureServices`:

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

配置应用程序的 web 主机，以使用 Windows 身份验证使用 HTTP.sys (*Program.cs*)。 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 在<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>命名空间。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。 Kerberos 和 HTTP.sys 不支持用户模式身份验证。 必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。 注册主机的服务主体名称 (SPN)，而不是应用的用户。

> [!NOTE]
> HTTP.sys 不支持 Nano Server 版本 1709年或更高版本上。 若要使用 Windows 身份验证和 HTTP.sys 与 Nano Server，使用[Server Core (microsoft/windowsservercore) 容器](https://hub.docker.com/r/microsoft/windowsservercore/)。 在 Server Core 上的详细信息，请参阅[什么是 Windows Server 中的服务器核心安装选项？](/windows-server/administration/server-core/what-is-server-core)。

## <a name="authorize-users"></a>为用户授权

匿名访问的配置状态确定的方式`[Authorize]`和`[AllowAnonymous]`应用中使用属性。 以下两个部分介绍如何处理的匿名访问权限的禁止和允许配置状态。

### <a name="disallow-anonymous-access"></a>不允许匿名访问

启用 Windows 身份验证，并且禁用了匿名访问，则`[Authorize]`和`[AllowAnonymous]`特性不起作用。 如果 IIS 站点配置为不允许匿名访问，请求将永远不会到达该应用程序。 出于此原因，`[AllowAnonymous]`属性不适用。

### <a name="allow-anonymous-access"></a>允许匿名访问

启用 Windows 身份验证和匿名访问，使用`[Authorize]`和`[AllowAnonymous]`属性。 `[Authorize]`特性，您可以保护的应用程序的要求进行身份验证的终结点。 `[AllowAnonymous]`特性重写`[Authorize]`允许匿名访问的应用中的属性。 属性使用情况详细信息，请参阅<xref:security/authorization/simple>。

> [!NOTE]
> 默认情况下，缺少授权可访问的页面的用户会显示 HTTP 403 响应为空。 [StatusCodePages 中间件](xref:fundamentals/error-handling#usestatuscodepages)可以配置为向用户提供更好的"拒绝访问"体验。

## <a name="impersonation"></a>Impersonation

ASP.NET Core 未实现模拟。 应用程序运行具有的所有请求，使用应用程序池或进程标识的应用的标识。 如果应用程序应执行代表用户操作，使用[WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)中[终端的内联中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)中`Startup.Configure`。 在此上下文中运行的单个操作，然后关闭上下文。

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` 不支持异步操作，不应该用于复杂的方案。 例如，包装整个请求或中间件链不受支持或推荐的。

::: moniker range=">= aspnetcore-3.0"

虽然[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)程序包可启用 Windows 上的身份验证，在 Windows 上仅支持 Linux 和 macOS 的模拟。

::: moniker-end

## <a name="claims-transformations"></a>声明转换

承载与 IIS 进程内模式时<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>不在内部调用以初始化用户。 因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。 有关详细信息和托管进程中时将激活声明转换的代码示例，请参阅<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>。

## <a name="additional-resources"></a>其他资源

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
