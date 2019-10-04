---
title: 将 ASP.NET Core 应用部署到 Azure 应用服务
author: guardrex
description: 本文包含 Azure 主机和部署资源的链接。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: bda4923adb0f9769f883ef64f7902c8650308222
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924893"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>将 ASP.NET Core 应用部署到 Azure 应用服务

[Azure 应用服务](https://azure.microsoft.com/services/app-service/)是一个用于托管 Web 应用（包括 ASP.NET Core）的 [Microsoft 云计算平台服务](https://azure.microsoft.com/)。

## <a name="useful-resources"></a>有用资源

Azure 应用文档、教程、示例、操作指南和其他资源由[应用服务文档](/azure/app-service/)提供。 两个有关托管 ASP.NET Core 应用的重要教程为：

[在 Azure 中创建 ASP.NET Core Web 应用](/azure/app-service/app-service-web-get-started-dotnet)  
使用 Visual Studio 创建 ASP.NET Core Web 应用，并将其部署到 Windows 上的 Azure 应用服务。

[在 Linux 上的应用服务中创建 ASP.NET Core 应用](/azure/app-service/containers/quickstart-dotnetcore)  
使用命令行创建 ASP.NET Core Web 应用，并将其部署到 Linux 上的 Azure 应用服务。

有关 Azure 应用服务上可用的 ASP.NET Core 版本，请参阅[应用服务仪表板上的 ASP.NET Core](https://aspnetcoreon.azurewebsites.net/)。

ASP.NET Core 文档中提供以下文章：

<xref:tutorials/publish-to-azure-webapp-using-vs>  
了解如何使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure 应用服务。

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
了解如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用 Git 将它部署到 Azure 应用服务以实现持续部署。

[创建第一个管道](/azure/devops/pipelines/get-started-yaml)  
为 ASP.NET Core 应用设置 CI 生成，然后创建针对 Azure 应用服务的持续部署发布。

[Azure Web 应用沙盒](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
探索 Azure Apps 平台强制实施的 Azure 应用服务运行时执行限制。

<xref:test/troubleshoot>  
理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。

## <a name="application-configuration"></a>应用程序配置

### <a name="platform"></a>Platform

::: moniker range=">= aspnetcore-2.2"

64 位 (x64) 和 32 位 (x86) 应用的运行时存在于 Azure 应用服务上。 应用服务上提供的 [.NET Core SDK](/dotnet/core/sdk) 为 32 位，但可使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 控制台或通过 Visual Studio 中的发布流程来部署本地构建的 64 位应用。 有关详细信息，请参阅[发布和部署应用](#publish-and-deploy-the-app)部分。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

对于具有本机依赖项的应用，32 位 (x86) 应用的运行时存在于 Azure 应用服务上。 应用服务上提供的 [.NET Core SDK](/dotnet/core/sdk) 为 32 位。

::: moniker-end

要详细了解 .NET Core 框架组件和分发方法（例如有关 .NET Core 运行时和 .NET Core SDK 的信息），请参阅[关于 .NET Core：撰写](/dotnet/core/about#composition)。

### <a name="packages"></a>package

包含以下 NuGet 包，以便为部署到 Azure 应用服务的应用提供自动日志记录功能：

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) 使用 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) 提供 ASP.NET Core 与 Azure 应用服务的启动集成。 添加的日志记录功能由 `Microsoft.AspNetCore.AzureAppServicesIntegration` 包提供。
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) 执行 [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics)，在 `Microsoft.Extensions.Logging.AzureAppServices` 包中添加 Azure 应用服务诊断日志记录提供程序。
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) 提供记录器实现，支持 Azure 应用服务诊断日志和日志流式处理功能。

[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不提供前述包。 面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包的应用必须显式引用应用的项目文件中的各个包。

## <a name="override-app-configuration-using-the-azure-portal"></a>使用 Azure 门户重写应用配置

Azure 门户中的应用设置允许为应用设置环境变量。 可以通过[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)来使用环境变量。

在 Azure 门户中创建或修改应用设置并选择“保存”按钮时，Azure 应用将重启  。 服务重启后，应用即可使用环境变量。

::: moniker range=">= aspnetcore-3.0"

当应用使用[通用主机](xref:fundamentals/host/generic-host)时，环境变量在默认情况下不会加载到应用的配置，且配置提供程序必须由开发人员添加。 在添加配置提供程序时，开发人员将确定环境变量前缀。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

当应用使用 [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 生成主机时，配置主机的环境变量会使用 `ASPNETCORE_`。 有关详细信息，请参阅 <xref:fundamentals/host/web-host> 和[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)。

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>代理服务器和负载均衡器方案

托管在[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)时配置转接头中间件的 [IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。 对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。 有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。

## <a name="monitoring-and-logging"></a>监视和日志记录

::: moniker range=">= aspnetcore-3.0"

部署到应用服务的 ASP.NET Core 应用会自动接收“ASP.NET Core 记录集成”这一应用服务扩展  。 借助该扩展，可记录 Azure 应用服务上针对 ASP.NET Core 应用的集成。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

部署到应用服务的 ASP.NET Core 应用自动接收应用服务扩展“ASP.NET Core 日志记录扩展”  。 借助该扩展，可记录 Azure 应用服务上针对 ASP.NET Core 应用的集成。

::: moniker-end

有关监视、日志记录和故障排除的信息，请参阅以下文章：

[监视 Azure 应用服务中的应用](/azure/app-service/web-sites-monitor)  
了解如何查看应用和应用服务计划的配额和指标。

[在 Azure 应用服务中为应用启用诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)  
了解如何启用和访问 HTTP 状态代码、失败请求和 Web 服务器活动的诊断日志记录。

<xref:fundamentals/error-handling>  
了解在 ASP.NET Core 应用中处理错误的常见方法。

<xref:test/troubleshoot-azure-iis>  
了解如何使用 ASP.NET Core 应用诊断 Azure 应用服务部署问题。

<xref:host-and-deploy/azure-iis-errors-reference>  
使用故障排除建议查看 Azure 应用服务/IIS 托管的应用的常见部署配置错误。

## <a name="data-protection-key-ring-and-deployment-slots"></a>数据保护密钥环和部署槽位

[数据保护密钥](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)保存在 %HOME%\ASP.NET\DataProtection-Keys 文件夹中  。 此文件夹由网络存储提供支持，并跨托管应用的所有计算机同步。 密钥不是静态保护的。 此文件夹向单个部署槽位中应用的所有实例提供密钥环。 各部署槽位（例如过渡槽和生成槽）不共享密钥环。

在部署槽位之间交换时，使用数据保护的任意系统都无法使用之前槽位中的密钥环来解密存储的数据。 ASP.NET Cookie 中间件使用数据保护来保护其 cookie。 这导致用户注销使用标准 ASP.NET Cookie 中间件的应用。 对于独立于槽位的密钥环解决方案，请使用外部密钥环提供程序，例如：

* Azure Blob 存储
* Azure Key Vault
* SQL 存储
* Redis 缓存

有关详细信息，请参阅 <xref:security/data-protection/implementation/key-storage-providers>。
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>
<!-- revert this after 3.0 supported
## Deploy ASP.NET Core preview release to Azure App Service

Use one of the following approaches if the app relies on a preview release of .NET Core:

* [Install the preview site extension](#install-the-preview-site-extension).
* [Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).
* [Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).
-->
## <a name="deploy-aspnet-core-30-to-azure-app-service"></a>将 ASP.NET Core 3.0 部署到 Azure 应用服务

我们希望尽快在 Azure 应用服务中提供 ASP.NET Core 3.0。

如果应用依赖于 .NET Core 3.0，请使用以下方法之一：

* [安装预览站点扩展](#install-the-preview-site-extension)。
* [部署独立式预览版应用](#deploy-a-self-contained-preview-app)。
* [对用于容器的 Web 应用使用 Docker](#use-docker-with-web-apps-for-containers)。

### <a name="install-the-preview-site-extension"></a>安装预览站点扩展

如果无法使用预览版站点扩展，请提出 [aspnet/AspNetCore 问题](https://github.com/aspnet/AspNetCore/issues)。

1. 从 Azure 门户导航到“应用服务”。
1. 选择 Web 应用。
1. 在搜索框中键入“ex”以筛选“扩展”，或向下滚动管理工具列表。
1. 选择“扩展”  。
1. 选择“添加”  。
1. 从列表选择“ASP.NET Core {X.Y} ({x64|x86}) 运行时”  扩展，其中 `{X.Y}` 是 ASP.NET Core 预览版本，并且 `{x64|x86}` 指定平台。
1. 选择“确定”  以接受法律条款。
1. 选择“确定”安装扩展  。

操作完成时，即表示已安装最新的 .NET Core 预览版。 验证安装：

1. 选择“高级工具”  。
1. 选择“高级工具”  中的“Go”  。
1. 选择“调试控制台”   > “PowerShell”  菜单项。
1. 从 PowerShell 命令提示符处执行以下命令。 在以下命令中，将 ASP.NET Core 运行时版本替换为 `{X.Y}`，并将平台替换为 `{PLATFORM}`：

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   如果安装 x64 预览版运行时，该命令将返回`True`。

> [!NOTE]
> 对于在 A 系列计算机上或更高级托管层上托管的应用，可在 Azure 门户中的应用设置中设置应用服务应用的平台体系结构 (x86/x64)。 如果应用在进程内模式下运行并且平台体系结构配置为 64 位 (x64)，则 ASP.NET Core 模块会使用 64 位预览版运行时（如存在）。 安装“ASP.NET Core {X.Y} (x64) 运行时”  扩展。
>
> 安装 x64 预览版运行时后，在 Kudu PowerShell 命令窗口中运行以下命令以验证该安装。 在以下命令中，将 ASP.NET Core 运行时版本替换为 `{X.Y}` ：
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> 如果安装 x64 预览版运行时，该命令将返回`True`。

> [!NOTE]
> ASP.NET Core 扩展  可为 Azure 应用服务上的 ASP.NET Core 启用附加功能，例如启用 Azure 日志记录。 从 Visual Studio 进行部署时，将自动安装该扩展。 如果未安装该扩展，请为应用安装它。

**通过 ARM 模板使用预览站点扩展**

如果使用 ARM 模板创建和部署应用，则可使用 `siteextensions` 资源类型将站点扩展添加到 Web 应用。 例如:

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a>部署独立式预览版应用

针对预览运行时的[自包含部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)在部署中承载预览运行时。

部署自包含应用时：

* Azure 应用服务中的站点不需要[预览站点扩展名](#install-the-preview-site-extension)。
* 必须使用不同于发布[依赖框架部署 (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd) 的方法发布应用。

按照[部署独立式应用](#deploy-the-app-self-contained)部分中的指南操作。

### <a name="use-docker-with-web-apps-for-containers"></a>对用于容器的 Web 应用使用 Docker

[Docker 中心](https://hub.docker.com/r/microsoft/aspnetcore/)包含最新的预览 Docker 映像。 这些映像可以用作基础映像。 按常规方法使用映像并部署到用于容器的 Web 应用。

## <a name="publish-and-deploy-the-app"></a>发布和部署应用

### <a name="deploy-the-app-framework-dependent"></a>部署依赖框架的应用

::: moniker range=">= aspnetcore-2.2"

对于 64 位[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：

* 使用 64 位 .NET Core SDK 部署 64 位的应用。
* 在应用服务的“配置” > “常规”l“设置”中将“平台”设置为“64 位”     。 应用必须使用基本服务计划或更高级别的服务计划才能选择平台位数。

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 从 Visual Studio 工具栏中选择“构建” > “发布 {应用程序名称}”，或者在解决方案资源管理器中右键单击项目，并选择“发布”     。
1. 在“选择发布目标”对话框中，确认已选中“应用服务”   。
1. 选择“高级”  。 随即会打开“发布”对话框  。
1. 在“发布”对话框中  ：
   * 确认已选中“发布”配置  。
   * 打开“部署模式”下拉列表，然后选择“依赖框架”   。
   * 选择“可移植”作为目标运行时   。
   * 如果需要在部署时删除其他文件，请打开“文件发布选项”，然后选中复选框以删除目标位置的其他文件  。
   * 选择“保存”  。
1. 按照发布向导的其余提示创建新站点或更新现有站点。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 在项目文件中，不要指定[运行时标识符 (RID)](/dotnet/core/rid-catalog)。

1. 在命令 shell 中，使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令在发布配置中发布应用。 在下例中，应用发布为依赖框架的应用：

   ```console
   dotnet publish --configuration Release
   ```

1. 将 bin/Release/{TARGET FRAMEWORK}/publish 目录的内容移动到应用服务中的站点  。 如果将 publish 文件夹内容从本地硬盘或网络共享直接拖动到 [Kudu](https://github.com/projectkudu/kudu/wiki) 控制台中的应用服务，则请将文件拖动到 Kudu 控制台的 `D:\home\site\wwwroot` 文件夹  。

---

### <a name="deploy-the-app-self-contained"></a>部署自包含应用

对[独立式部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)使用 Visual Studio 或命令行界面 (CLI) 工具。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 从 Visual Studio 工具栏中选择“构建” > “发布 {应用程序名称}”，或者在解决方案资源管理器中右键单击项目，并选择“发布”     。
1. 在“选择发布目标”对话框中，确认已选中“应用服务”   。
1. 选择“高级”  。 随即会打开“发布”对话框  。
1. 在“发布”对话框中  ：
   * 确认已选中“发布”配置  。
   * 打开“部署模式”下拉列表，然后选择“自包含”   。
   * 从“目标运行时”下拉列表中选择目标运行时  。 默认值为 `win-x86`。
   * 如果需要在部署时删除其他文件，请打开“文件发布选项”，然后选中复选框以删除目标位置的其他文件  。
   * 选择“保存”  。
1. 按照发布向导的其余提示创建新站点或更新现有站点。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 在项目文件中，指定一个或多个[运行时标识符 (RID)](/dotnet/core/rid-catalog)。 对单个 RID 使用 `<RuntimeIdentifier>`（单数），或使用 `<RuntimeIdentifiers>`（复数）提供以分号分隔的 RID 列表。 在以下示例中，指定 `win-x86` RID：

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. 在命令 shell 中，使用 [dotnet publish ](/dotnet/core/tools/dotnet-publish) 命令在主机运行时的发布配置中发布应用。 在以下示例中，为 `win-x86` RID 发布应用。 提供给 `--runtime` 选项的 RID 必须在项目文件的 `<RuntimeIdentifier>`（或 `<RuntimeIdentifiers>`）属性中提供。

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. 将 bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish 目录的内容移动到应用服务中的站点  。 如果将 publish 文件夹内容从本地硬盘或网络共享直接拖动到 Kudu 控制台中的应用服务，则请将文件拖动到 Kudu 控制台的 `D:\home\site\wwwroot` 文件夹  。

---

## <a name="protocol-settings-https"></a>协议设置 (HTTPS)

借助安全的协议绑定，可在通过 HTTPS 响应请求时指定要使用的证书。 若要绑定，需要一个为特定主机名颁发的有效专用证书 ( *.pfx*)。 有关详细信息，请参阅[教程：将现有自定义 SSL 证书绑定到 Azure 应用服务](/azure/app-service/app-service-web-tutorial-custom-ssl)。

## <a name="transform-webconfig"></a>转换 web.config

如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。

## <a name="additional-resources"></a>其他资源

* [应用服务概述](/azure/app-service/app-service-web-overview)
* [Azure 应用服务：托管 .NET 应用的最佳位置（55 分钟概览视频）](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Azure 应用服务诊断概述](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Windows Server 上的 Azure 应用服务使用 [Internet Information Services (IIS)](https://www.iis.net/)。 以下是基础 IIS 技术的相关主题：

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server - 当前和以前版本的 IT 管理员内容](/windows-server/windows-server-versions)
