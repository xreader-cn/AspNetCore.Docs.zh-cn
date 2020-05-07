---
title: Visual Studio 中针对 ASP.NET Core 的开发时 IIS 支持
author: rick-anderson
description: 发现对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的支持。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: a6719b4f84b1bc60c7c2aea2aa3a97ef79f43e2e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777015"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Visual Studio 中针对 ASP.NET Core 的开发时 IIS 支持

作者：[Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

本文介绍了对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的 [Visual Studio](https://visualstudio.microsoft.com) 支持。 本主题逐步介绍了如何启用此方案并设置项目。

## <a name="prerequisites"></a>先决条件

* [Visual Studio（适用于 Windows）](https://visualstudio.microsoft.com/downloads/)
* ASP.NET 和 Web 开发工作负荷 
* .NET Core 跨平台开发工作负荷 
* X.509 安全证书（用于 HTTPS 支持）

## <a name="enable-iis"></a>启用 IIS

1. 在 Windows 中，导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。
1. 选中“Internet Information Services”  复选框。 选择“确定”  。

IIS 安装可能需要重启系统。

## <a name="configure-iis"></a>配置 IIS

IIS 必须具有具备以下配置的网站：

* **主机名** &ndash; 通常情况下，使用“主机名”  为“`localhost`”的“默认网站”  。 不过，任何具有唯一主机名的有效 IIS 网站也都可行。
* **网站绑定**
  * 对于要求使用 HTTPS 的应用，请使用证书创建对端口 443 的绑定。 通常使用的是“IIS Express 开发证书”  ，但任何有效证书也都可行。
  * 对于使用 HTTP 的应用，请确认是否存在对端口 80 的绑定，或为新网站创建对端口 80 的绑定。
  * 对 HTTP 或 HTTPS 使用单一绑定。 不支持同时绑定到 HTTP 和 HTTPS 端口  。

## <a name="enable-development-time-iis-support-in-visual-studio"></a>在 Visual Studio 中启用开发时 IIS 支持

1. 启动 Visual Studio 安装程序。
1. 对于计划用于 IIS 开发时支持的 Visual Studio 安装，选择“修改”  。
1. 对于 ASP.NET 和 Web 开发  工作负荷，找到并安装“开发时 IIS 支持”  组件。

   在工作负荷右侧的“安装详细信息”  面板中，“开发时 IIS 支持”  下的“可选”  部分列出了此组件。 该组件将安装 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，该模块是运行具有 IIS 的 ASP.NET Core 应用所需的本机 IIS 模块。

## <a name="configure-the-project"></a>配置项目

### <a name="https-redirection"></a>HTTPS 重定向

对于要求使用 HTTPS 的新项目，选中“新建 ASP.NET Core Web 应用”  窗口中的“针对 HTTPS 进行配置”  复选框。 选中此复选框会向创建后的应用添加 [HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)。

对于要求使用 HTTPS 的现有项目，使用 `Startup.Configure` 中的 HTTPS 重定向和 HSTS 中间件。 有关详细信息，请参阅 <xref:security/enforcing-ssl>。

对于使用 HTTP 的项目，[HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)不会添加到应用中。 无需配置应用。

### <a name="iis-launch-profile"></a>IIS 启动配置文件

创建新的启动配置文件以添加开发时 IIS 支持：

1. 在“解决方案资源管理器”  中，右键单击项目。 选择“属性”  。 打开“调试”  选项卡。
1. 对于配置文件  ，请选择“新建”  按钮。 在弹出窗口中将该配置文件命名为“IIS”。 选择“确定”创建配置文件  。
1. 对于“启动”  设置，请从列表中选择“IIS”  。
1. 选中“启动浏览器”  复选框并提供终结点 URL。

   如果应用要求使用 HTTPS，使用 HTTPS 终结点 (`https://`)。 如果应用要求使用 HTTP，使用 HTTP (`http://`) 终结点。

   提供[前面指定的 IIS 配置](#configure-iis)使用的相同主机名和端口，通常为 `localhost`。

   在 URL 结尾处提供应用名称。

   例如，`https://localhost/WebApplication1` (HTTPS) 或 `http://localhost/WebApplication1` (HTTP) 是有效的终结点 URL。
1. 在“环境变量”  部分中，选择“添加”  按钮。 提供“名称”  为“`ASPNETCORE_ENVIRONMENT`”且“值”  为“`Development`”的环境变量。
1. 在“Web 服务器设置”  区域中，将“应用 URL”  设置为用于“启动浏览器”  终结点 URL 的相同值。
1. 对于 Visual Studio 2019 或更高版本中的“托管模型”  设置，选择“默认”  ，以使用项目使用的托管模型。 如果项目在自己的项目文件中设置 `<AspNetCoreHostingModel>` 属性，使用的是此属性的值（`InProcess` 或 `OutOfProcess`）。 如果没有设置此属性，使用的是应用的默认（进程内）托管模型。 如果应用需要不同于常规托管模型的显式托管模型，请根据需要将“托管模型”  设置为“`In Process`”或“`Out Of Process`”。
1. 保存配置文件。

如果未使用 Visual Studio，请将启动配置文件手动添加到“属性”  文件夹中的 [launchSettings.json](https://json.schemastore.org/launchsettings) 文件内。 下面的示例展示了如何将配置文件配置为使用 HTTPS 协议：

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

确认 `applicationUrl` 和 `launchUrl` 终结点是否一致，且是否使用与 IIS 绑定配置相同的协议（HTTP 或 HTTPS）。

## <a name="run-the-project"></a>运行该项目

以管理员身份运行 Visual Studio：

* 确认已将生成配置下拉列表设置为“调试”  。
* 将[“开始调试”按钮](/visualstudio/debugger/debugger-feature-tour)设置为 IIS 配置文件，然后选择此按钮以启动该应用  。

如果不以管理员身份运行，Visual Studio 可能会提示重启。 如果出现提示，请重启 Visual Studio。

如果使用不受信任的开发证书，则浏览器可能会要求你为不受信任的证书创建异常。

> [!NOTE]
> 通过[仅我的代码](/visualstudio/debugger/just-my-code)调试“发布”生成配置，从而导致降低编译器优化体验。 例如，不会遇到断点。

## <a name="additional-resources"></a>其他资源

* [IIS 中 IIS 管理器入门](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文介绍了对调试在 Windows Server 上与 IIS 一起运行的 ASP.NET Core 应用的 [Visual Studio](https://visualstudio.microsoft.com) 支持。 本主题逐步介绍了如何启用此方案并设置项目。

## <a name="prerequisites"></a>先决条件

* [Visual Studio（适用于 Windows）](https://visualstudio.microsoft.com/downloads/)
* ASP.NET 和 Web 开发工作负荷 
* .NET Core 跨平台开发工作负荷 
* X.509 安全证书（用于 HTTPS 支持）

## <a name="enable-iis"></a>启用 IIS

1. 在 Windows 中，导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）     。
1. 选中“Internet Information Services”  复选框。 选择“确定”  。

IIS 安装可能需要重启系统。

## <a name="configure-iis"></a>配置 IIS

IIS 必须具有具备以下配置的网站：

* **主机名** &ndash; 通常情况下，使用“主机名”  为“`localhost`”的“默认网站”  。 不过，任何具有唯一主机名的有效 IIS 网站也都可行。
* **网站绑定**
  * 对于要求使用 HTTPS 的应用，请使用证书创建对端口 443 的绑定。 通常使用的是“IIS Express 开发证书”  ，但任何有效证书也都可行。
  * 对于使用 HTTP 的应用，请确认是否存在对端口 80 的绑定，或为新网站创建对端口 80 的绑定。
  * 对 HTTP 或 HTTPS 使用单一绑定。 不支持同时绑定到 HTTP 和 HTTPS 端口  。

## <a name="enable-development-time-iis-support-in-visual-studio"></a>在 Visual Studio 中启用开发时 IIS 支持

1. 启动 Visual Studio 安装程序。
1. 对于计划用于 IIS 开发时支持的 Visual Studio 安装，选择“修改”  。
1. 对于 ASP.NET 和 Web 开发  工作负荷，找到并安装“开发时 IIS 支持”  组件。

   在工作负荷右侧的“安装详细信息”  面板中，“开发时 IIS 支持”  下的“可选”  部分列出了此组件。 该组件将安装 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，该模块是运行具有 IIS 的 ASP.NET Core 应用所需的本机 IIS 模块。

## <a name="configure-the-project"></a>配置项目

### <a name="https-redirection"></a>HTTPS 重定向

对于要求使用 HTTPS 的新项目，选中“新建 ASP.NET Core Web 应用”  窗口中的“针对 HTTPS 进行配置”  复选框。 选中此复选框会向创建后的应用添加 [HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)。

对于要求使用 HTTPS 的现有项目，使用 `Startup.Configure` 中的 HTTPS 重定向和 HSTS 中间件。 有关详细信息，请参阅 <xref:security/enforcing-ssl>。

对于使用 HTTP 的项目，[HTTPS 重定向和 HSTS 中间件](xref:security/enforcing-ssl)不会添加到应用中。 无需配置应用。

### <a name="iis-launch-profile"></a>IIS 启动配置文件

创建新的启动配置文件以添加开发时 IIS 支持：

1. 在“解决方案资源管理器”  中，右键单击项目。 选择“属性”  。 打开“调试”  选项卡。
1. 对于配置文件  ，请选择“新建”  按钮。 在弹出窗口中将该配置文件命名为“IIS”。 选择“确定”创建配置文件  。
1. 对于“启动”  设置，请从列表中选择“IIS”  。
1. 选中“启动浏览器”  复选框并提供终结点 URL。

   如果应用要求使用 HTTPS，使用 HTTPS 终结点 (`https://`)。 如果应用要求使用 HTTP，使用 HTTP (`http://`) 终结点。

   提供[前面指定的 IIS 配置](#configure-iis)使用的相同主机名和端口，通常为 `localhost`。

   在 URL 结尾处提供应用名称。

   例如，`https://localhost/WebApplication1` (HTTPS) 或 `http://localhost/WebApplication1` (HTTP) 是有效的终结点 URL。
1. 在“环境变量”  部分中，选择“添加”  按钮。 提供“名称”  为“`ASPNETCORE_ENVIRONMENT`”且“值”  为“`Development`”的环境变量。
1. 在“Web 服务器设置”  区域中，将“应用 URL”  设置为用于“启动浏览器”  终结点 URL 的相同值。
1. 对于 Visual Studio 2019 或更高版本中的“托管模型”  设置，选择“默认”  ，以使用项目使用的托管模型。 如果项目在自己的项目文件中设置 `<AspNetCoreHostingModel>` 属性，使用的是此属性的值（`InProcess` 或 `OutOfProcess`）。 如果没有设置此属性，使用的是应用的默认（进程外）托管模型。 如果应用需要不同于常规托管模型的显式托管模型，请根据需要将“托管模型”  设置为“`In Process`”或“`Out Of Process`”。
1. 保存配置文件。

如果未使用 Visual Studio，请将启动配置文件手动添加到“属性”  文件夹中的 [launchSettings.json](https://json.schemastore.org/launchsettings) 文件内。 下面的示例展示了如何将配置文件配置为使用 HTTPS 协议：

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

确认 `applicationUrl` 和 `launchUrl` 终结点是否一致，且是否使用与 IIS 绑定配置相同的协议（HTTP 或 HTTPS）。

## <a name="run-the-project"></a>运行该项目

以管理员身份运行 Visual Studio：

* 确认已将生成配置下拉列表设置为“调试”  。
* 将[“开始调试”按钮](/visualstudio/debugger/debugger-feature-tour)设置为 IIS 配置文件，然后选择此按钮以启动该应用  。

如果不以管理员身份运行，Visual Studio 可能会提示重启。 如果出现提示，请重启 Visual Studio。

如果使用不受信任的开发证书，则浏览器可能会要求你为不受信任的证书创建异常。

> [!NOTE]
> 通过[仅我的代码](/visualstudio/debugger/just-my-code)调试“发布”生成配置，从而导致降低编译器优化体验。 例如，不会遇到断点。

## <a name="additional-resources"></a>其他资源

* [IIS 中 IIS 管理器入门](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
