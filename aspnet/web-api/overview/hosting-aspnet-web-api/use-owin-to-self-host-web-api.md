---
uid: web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
title: 使用 OWIN 自托管 ASP.NET Web API |Microsoft Docs
author: rick-anderson
description: 本教程演示如何使用 OWIN 自托管 Web API 框架的控制台应用程序中托管 ASP.NET Web API。 打开 Web Interface for.NET (OWIN) d...
ms.author: riande
ms.date: 07/09/2013
ms.assetid: a90a04ce-9d07-43ad-8250-8a92fb2bd3d5
msc.legacyurl: /web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
msc.type: authoredcontent
ms.openlocfilehash: 59ce24aa47ca590fbe9b617dbbe8bc6b3711849e
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667383"
---
<a name="use-owin-to-self-host-aspnet-web-api"></a>使用 OWIN 自托管 ASP.NET Web API 
====================

> 本教程演示如何使用 OWIN 自托管 Web API 框架的控制台应用程序中托管 ASP.NET Web API。
>
> [用于.NET 开放 Web 接口](http://owin.org)(OWIN) 定义.NET web 服务器和 web 应用程序之间的抽象。 OWIN 将从服务器中，这使 OWIN 适合于自承载在 IIS 外部自己进程中的 web 应用程序的 web 应用程序中分离出来。
>
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 
> - Web API 5.2.7


> [!NOTE]
> 您可以在本教程中找到完整的源代码[aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt)。


## <a name="create-a-console-application"></a>创建控制台应用程序

上**文件**菜单中，**新建**，然后选择**项目**。 从**已安装**下**Visual C#** ，选择**Windows Desktop** ，然后选择**控制台应用 (.Net Framework)**。 将项目命名为"OwinSelfhostSample"，然后选择**确定**。

[![](use-owin-to-self-host-web-api/_static/image7.png)](use-owin-to-self-host-web-api/_static/image7.png)

## <a name="add-the-web-api-and-owin-packages"></a>添加 Web API 和 OWIN 包

从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。 在包管理器控制台窗口中，输入以下命令：

`Install-Package Microsoft.AspNet.WebApi.OwinSelfHost`

这将安装 WebAPI OWIN selfhost 包和所有所需的 OWIN 包。

[![](use-owin-to-self-host-web-api/_static/image4.png)](use-owin-to-self-host-web-api/_static/image3.png)

## <a name="configure-web-api-for-self-host"></a>配置 Web API 的自托管

在解决方案资源管理器，右键单击该项目并选择**外** / **类**以添加一个新类。 将此类命名为 `Startup`。

![](use-owin-to-self-host-web-api/_static/image5.png)

使用以下内容替换所有样板代码，此文件中：

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample1.cs)]

## <a name="add-a-web-api-controller"></a>添加 Web API 控制器

接下来，添加 Web API 控制器类。 在解决方案资源管理器，右键单击该项目并选择**外** / **类**以添加一个新类。 将此类命名为 `ValuesController`。

使用以下内容替换所有样板代码，此文件中：

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample2.cs)]

## <a name="start-the-owin-host-and-make-a-request-with-httpclient"></a>启动 OWIN 主机并使 HttpClient 的请求

使用以下内容替换所有 Program.cs 文件中的样板代码：

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample3.cs)]

## <a name="run-the-application"></a>运行此应用程序

若要运行该应用程序，请在 Visual Studio 中按 F5。 输出应如下所示：

[!code-console[Main](use-owin-to-self-host-web-api/samples/sample4.cmd)]

![](use-owin-to-self-host-web-api/_static/image6.png)

## <a name="additional-resources"></a>其他资源

[项目 Katana 概述](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)

[承载在 Azure 辅助角色中的 ASP.NET Web API](host-aspnet-web-api-in-an-azure-worker-role.md)
