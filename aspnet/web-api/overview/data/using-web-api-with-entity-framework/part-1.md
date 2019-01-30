---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: 通过 Entity Framework 6 使用 Web API 2 |Microsoft Docs
author: MikeWasson
description: 本教程将讲述使用 ASP.NET Web API 创建 web 应用程序的基础知识后端。 本教程使用 Entity Framework 6 的数据布局...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236518"
---
<a name="using-web-api-2-with-entity-framework-6"></a>通过 Entity Framework 6 使用 Web API 2
====================

[下载已完成的项目](https://github.com/MikeWasson/BookService)

> 本教程介绍使用 ASP.NET Web API 创建 web 应用程序的基础知识后端。 本教程使用 Entity Framework 6 进行的数据层和 Knockout.js 进行客户端的 JavaScript 应用程序。 本教程还演示如何将应用部署到 Azure 应用服务 Web 应用。
>
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
>
> - Web API 2.1
> - Visual Studio 2017 (下载 Visual Studio 2017[此处](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))
> - Entity Framework 6
> - .NET 4.7
> - [Knockout.js](http://knockoutjs.com/) 3.1

本教程中使用 Entity Framework 6 与 ASP.NET Web API 2 创建操作后端数据库的 web 应用程序。 下面是您将创建的应用程序的屏幕截图。

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

该应用使用单页面应用程序 (SPA) 设计。 "单页面应用程序"是加载单个 HTML 页面，然后页面动态更新，而不是加载新页面的 web 应用程序的常规术语。 初始页面加载后该应用通过 AJAX 请求与服务器通信。 AJAX 请求返回 JSON 数据时，应用程序用它来更新 UI。

AJAX 并不新鲜，但如今有更加轻松地构建和维护大型复杂的 SPA 应用程序的 JavaScript 框架。 本教程使用[Knockout.js](http://knockoutjs.com/)，但您可以使用任何 JavaScript 客户端框架。

下面是此应用的主要构建基块：

- ASP.NET MVC 创建 HTML 页面。
- ASP.NET Web API 处理 AJAX 请求，并返回 JSON 数据。
- Knockout.js 数据绑定 HTML 元素的 JSON 数据。
- 实体框架与数据库通信。

## <a name="see-this-app-running-on-azure"></a>请参阅在 Azure 上运行此应用程序

若要查看已完成的站点作为实时 web 应用运行吗？ 通过选择下面的按钮，可以将完整版本的应用部署到 Azure 帐户。

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

需要一个 Azure 帐户才能将此解决方案部署到 Azure。 如果你还没有帐户，你具有以下选项：

- [免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604)-获取信用额度可用于试用付费版 Azure 服务，甚至在用之后最多可以保留帐户并使用免费的 Azure 服务。
- [激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604)-MSDN 订阅提供信用额度可以用于付费版 Azure 服务的每个月。

## <a name="create-the-project"></a>创建项目

打开 Visual Studio。 从**文件**菜单中，选择**新建**，然后选择**项目**。 (或选择**新的项目**起始页上。)

在**新的项目**对话框中，选择**Web**左窗格中并**ASP.NET Web 应用程序 (.NET Framework)** 在中间窗格中。 将项目命名**BookService** ，然后选择**确定**。

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

在中**新建 ASP.NET 项目**对话框中，选择**Web API**模板。

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


选择“确定”创建项目。

## <a name="configure-azure-settings-optional"></a>配置 Azure 设置 （可选）

创建项目后，你可以选择在任何时候将部署到 Azure 应用服务 Web 应用。 

1. 在解决方案资源管理器中右键单击项目，然后选择**发布**。

2. 在出现的窗口，选择**启动**。 **选取发布目标**对话框随即出现。

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. 选择“创建配置文件”。 “创建应用服务”对话框随即显示。

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   接受默认值，或输入应用程序名称、 资源组，托管计划、 Azure 订阅和地理区域的不同值。 

4. 选择**创建 SQL 数据库**。 **配置 SQL Server**对话框随即出现。 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   接受默认值或输入不同的值。 输入**管理员用户名**并**管理员密码**为新的数据库。 选择**确定**完成后。 **创建应用服务**页上再次出现。

5. 选择**创建**创建你的配置文件。 指示部署正在进行的右下角出现一条消息。 之后不久，**发布**窗口重新出现。

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    创建要部署的应用的配置文件现已推出。 


> [!div class="step-by-step"]
> [下一页](part-2.md)
