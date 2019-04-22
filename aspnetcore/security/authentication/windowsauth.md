---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: scottaddie
description: 了解如何在 ASP.NET Core，使用 IIS Express、 IIS 和 HTTP.sys 中配置 Windows 身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/03/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: bd4ffa79c4d1e0070c820fa9c06b0a84c3aaae74
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468648"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>在 ASP.NET Core 中配置 Windows 身份验证

通过[Scott Addie](https://twitter.com/Scott_Addie)和[Luke Latham](https://github.com/guardrex)

[Windows 身份验证](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)可以配置为使用托管的 ASP.NET Core 应用[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)。

Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。 使用 Active Directory 域标识或 Windows 帐户标识的用户在公司网络上运行你的服务器时，可以使用 Windows 身份验证。 Windows 身份验证是最适合于用户、 客户端应用程序和 web 服务器属于同一个 Windows 域的 intranet 环境。

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>启用 Windows 身份验证在 ASP.NET Core 应用

**Web 应用程序**模板可通过 Visual Studio 或.NET Core CLI 可以配置为支持 Windows 身份验证。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a>新的项目中使用 Windows 身份验证应用程序模板

在 Visual Studio 中：

1. 创建新项目。
1. 选择“ASP.NET Core Web 应用程序”。 选择“下一步”。
1. 中提供名称**项目名称**字段。 确认**位置**条目是否正确，或提供项目的位置。 选择“创建”。
1. 选择**更改**下**身份验证**。
1. 在中**更改身份验证**窗口中，选择**Windows 身份验证**。 选择 **确定**。
1. 选择“Web 应用程序”。
1. 选择“创建”。

运行应用。 用户名将显示在呈现的应用程序用户界面。

### <a name="manual-configuration-for-an-existing-project"></a>手动配置为现有项目的

项目的属性，您可以启用 Windows 身份验证并禁用匿名身份验证：

1. 右键单击 Visual Studio 中的项目**解决方案资源管理器**，然后选择**属性**。
1. 选择“调试”选项卡。
1. 清除的复选框**启用匿名身份验证**。
1. 选中的复选框**启用 Windows 身份验证**。

或者，可以在配置属性`iisSettings`的节点*launchSettings.json*文件：

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

使用**Windows 身份验证**应用模板。

执行[dotnet 新](/dotnet/core/tools/dotnet-new)命令`webapp`自变量 （ASP.NET Core Web 应用） 和`--auth Windows`切换：

```console
dotnet new webapp --auth Windows
```

---

在修改现有项目，确认项目文件包含的包引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)**或** [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 包。

## <a name="enable-windows-authentication-with-iis"></a>启用与 IIS Windows 身份验证

IIS 使用[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)到承载 ASP.NET Core 应用程序。 Windows 身份验证配置为通过 IIS *web.config*文件。 以下部分介绍如何：

* 提供本地*web.config*在部署应用时在服务器激活 Windows 身份验证的文件。
* 使用 IIS 管理器配置*web.config*已部署到服务器的 ASP.NET Core 应用的文件。

### <a name="iis-configuration"></a>IIS 配置

如果尚未执行此操作，使 IIS 能够承载 ASP.NET Core 应用程序。 有关详细信息，请参阅 <xref:host-and-deploy/iis/index>。

启用 Windows 身份验证的 IIS 角色服务。 有关详细信息，请参阅[IIS 角色服务 （请参阅步骤 2） 中启用 Windows 身份验证](xref:host-and-deploy/iis/index#iis-configuration)。

默认情况下，IIS 集成中间件配置为自动进行身份验证请求。 有关详细信息，请参阅[与 IIS 的 Windows 上托管 ASP.NET Core:IIS 选项 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)。

默认情况下，ASP.NET Core 模块配置为转发到应用的 Windows 身份验证令牌。 有关详细信息，请参阅[ASP.NET Core 模块配置参考：AspNetCore 元素的特性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。

### <a name="create-a-new-iis-site"></a>创建新的 IIS 站点

指定的名称和文件夹，并允许它创建新的应用程序池。

### <a name="enable-windows-authentication-for-the-app-in-iis"></a>启用 IIS 中应用的 Windows 身份验证

使用**任一**以下方法之一：

* [发布应用程序之前对开发端配置](#development-side-configuration-with-a-local-webconfig-file)(*推荐*)
* [发布应用后端服务器的配置](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a>开发端配置与本地 web.config 文件

执行以下步骤**之前**您[发布和部署你的项目](#publish-and-deploy-your-project-to-the-iis-site-folder)。

添加以下*web.config*到项目根目录的文件：

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

由 SDK 发布项目时 (而无需`<IsTransformWebConfigDisabled>`属性设置为`true`项目文件中)，已发布*web.config*文件包括`<location><system.webServer><security><authentication>`部分。 有关详细信息`<IsTransformWebConfigDisabled>`属性，请参阅<xref:host-and-deploy/iis/index#webconfig-file>。

#### <a name="server-side-configuration-with-the-iis-manager"></a>服务器端配置使用 IIS 管理器

执行以下步骤**后**您[发布和部署你的项目](#publish-and-deploy-your-project-to-the-iis-site-folder)。

1. 在 IIS 管理器中，选择下的 IIS 站点**站点**的节点**连接**侧栏。
1. 双击**身份验证**中**IIS**区域。
1. 选择**匿名身份验证**。 选择**禁用**中**操作**侧栏。
1. 选择**Windows 身份验证**。 选择**启用**中**操作**侧栏。

IIS 管理器时执行这些操作，会应用的修改*web.config*文件。 一个`<system.webServer><security><authentication>`使用更新的设置的添加节点`anonymousAuthentication`和`windowsAuthentication`:

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

`<system.webServer>`部分添加到*web.config*文件由 IIS 管理器是在应用外`<location>`发布应用时由.NET Core SDK 添加的部分。 因为部分添加之外`<location>`节点中，设置由任何继承[子应用](xref:host-and-deploy/iis/index#sub-applications)到当前应用。 若要阻止继承，将在添加`<security>`内的部分`<location><system.webServer>`SDK 提供的部分。

当使用 IIS 管理器添加 IIS 配置时，它只影响应用程序的*web.config*在服务器上的文件。 应用程序的后续部署可能会覆盖服务器上的设置，如果服务器的副本*web.config*替换为项目的*web.config*文件。 使用**任一**以下方法来管理的设置之一：

* 使用 IIS 管理器中的设置重置*web.config*文件后部署上覆盖该文件。
* 添加*web.config 文件*向本地使用的设置应用程序。 有关详细信息，请参阅[开发端配置](#development-side-configuration-with-a-local-webconfig-file)部分。

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a>发布并将项目部署到 IIS 的站点文件夹

使用 Visual Studio 或.NET Core CLI，发布，并将应用部署到目标文件夹。

有关使用 IIS 承载的详细信息，发布和部署，请参阅以下主题：

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

启动应用程序以验证 Windows 身份验证正常工作。

## <a name="enable-windows-authentication-with-httpsys"></a>启用 Windows 身份验证使用 HTTP.sys

虽然 Kestrel 不支持 Windows 身份验证，您可以使用[HTTP.sys](xref:fundamentals/servers/httpsys)以在 Windows 上支持自承载的方案。 下面的示例配置以使用 Windows 身份验证使用 HTTP.sys 的应用程序的 web 主机：

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。 Kerberos 和 HTTP.sys 不支持用户模式身份验证。 必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。 注册主机的服务主体名称 (SPN)，而不是应用的用户。

> [!NOTE]
> HTTP.sys 不支持 Nano Server 版本 1709年或更高版本上。 若要使用 Windows 身份验证和 HTTP.sys 与 Nano Server，使用[Server Core (microsoft/windowsservercore) 容器](https://hub.docker.com/r/microsoft/windowsservercore/)。 在 Server Core 上的详细信息，请参阅[什么是 Windows Server 中的服务器核心安装选项？](/windows-server/administration/server-core/what-is-server-core)。

## <a name="work-with-windows-authentication"></a>使用 Windows 身份验证

匿名访问的配置状态确定的方式`[Authorize]`和`[AllowAnonymous]`应用中使用属性。 以下两个部分介绍如何处理的匿名访问权限的禁止和允许配置状态。

### <a name="disallow-anonymous-access"></a>不允许匿名访问

启用 Windows 身份验证，并且禁用了匿名访问，则`[Authorize]`和`[AllowAnonymous]`特性不起作用。 如果 IIS 站点 （或 HTTP.sys） 配置为不允许匿名访问，请求将永远不会到达您的应用程序。 出于此原因，`[AllowAnonymous]`属性不适用。

### <a name="allow-anonymous-access"></a>允许匿名访问

启用 Windows 身份验证和匿名访问，使用`[Authorize]`和`[AllowAnonymous]`属性。 `[Authorize]`属性允许你保护部分，该应用程序的真正需要 Windows 身份验证。 `[AllowAnonymous]`特性重写`[Authorize]`特性允许匿名访问的应用中的使用情况。 请参阅[简单授权](xref:security/authorization/simple)属性使用情况详细信息。

在 ASP.NET Core 2.x，请`[Authorize]`属性需要进行额外配置中的*Startup.cs*以进行 Windows 身份验证质询匿名请求。 建议的配置略有根据正在使用的 web 服务器而异。

> [!NOTE]
> 默认情况下，缺少授权可访问的页面的用户会显示 HTTP 403 响应为空。 [StatusCodePages 中间件](xref:fundamentals/error-handling#usestatuscodepages)可以配置为向用户提供更好的"拒绝访问"体验。

#### <a name="iis"></a>IIS

如果使用 IIS，添加以下代码到`ConfigureServices`方法：

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

如果使用 HTTP.sys，添加以下代码到`ConfigureServices`方法：

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>Impersonation

ASP.NET Core 未实现模拟。 应用程序运行具有的所有请求，使用应用程序池或进程标识的应用的标识。 如果您需要显式执行代表用户操作，使用[WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)中[终端的内联中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)中`Startup.Configure`。 在此上下文中运行的单个操作，然后关闭上下文。

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` 不支持异步操作，不应该用于复杂的方案。 例如，包装整个请求或中间件链不受支持或推荐的。

### <a name="claims-transformations"></a>声明转换

承载与 IIS 进程内模式时<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>不在内部调用以初始化用户。 因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。 有关详细信息和托管进程中时将激活声明转换的代码示例，请参阅<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>。
