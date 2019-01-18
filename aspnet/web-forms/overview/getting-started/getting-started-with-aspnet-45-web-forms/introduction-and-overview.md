---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Getting Started with ASP.NET 4.7 Web 窗体和 Visual Studio 2017 |Microsoft Docs
author: Erikre
description: 此分步教程系列将指导您学习生成使用 ASP.NET 4.7 和 Microsoft Visual Studio 的 ASP.NET Web 窗体应用程序的基础知识
ms.author: riande
ms.date: 01/09/2019
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: fb41ce72e9454d8d670a0b95234d2bc3f909f0ee
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341545"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a>开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2017
====================

[下载 Wingtip Toys 示例项目 (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409)或[下载电子书 (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

本系列教程演示如何构建具有 ASP.NET 4.5 和 Microsoft Visual Studio 2017 的 ASP.NET Web 窗体应用程序。 

## <a name="introduction"></a>介绍

本教程系列将指导您创建使用 Visual Studio 2017 和 ASP.NET 4.5 的 ASP.NET Web 窗体应用程序。 你将创建名为应用程序**Wingtip Toys** -销售联机项简化店面 web 站点。 系列中，突出显示新的 ASP.NET 4.5 功能。

### <a name="target-audience"></a>目标受众

本系列教程的目标受众是开发人员熟悉 ASP.NET Web 窗体。

您应该对一些了解以下几个方面：

- 面向对象的编程 (OOP) 和语言
- Web 开发 (HTML、 CSS、 JavaScript)
- 关系数据库
- N 层体系结构

若要查看这些区域，请考虑学习以下内容：

- [Visual C# 入门](https://msdn.microsoft.com/library/a72418yk.aspx)
- [Web 开发](https://msdn.microsoft.com/beginner/bb308760.aspx)， [HTML、 CSS、 JavaScript、 SQL、 PHP、 JQuery](http://w3schools.com/)
- [关系数据库](http://en.wikipedia.org/wiki/Relational_database)
- [多层体系结构](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a>应用程序功能

本系列中显示的 ASP.NET Web 窗体功能包括：

- Web 应用程序项目 （而不是网站项目）
- Web Forms — Web 窗体
- 母版页配置
- Bootstrap
- 实体框架代码优先，LocalDB
- 请求验证
- 强类型数据控件
- 模型绑定
- 数据注释
- 值提供程序
- SSL 和 OAuth
- ASP.NET 标识、 配置和授权
- 非介入式验证
- 路由
- ASP.NET 错误处理

### <a name="application-scenarios-and-tasks"></a>应用程序方案和任务

教程系列的任务包括：

- 创建、 检查，并运行新的项目
- 创建的数据库结构
- 初始化和数据库进行种子设定
- 自定义 UI 样式、 图形和母版页
- 添加页面和导航
- 显示菜单的详细信息和产品数据
- 创建购物车
- 添加 SSL 和 OAuth 支持
- 添加付款方式
- 包括一个管理员角色和用户访问应用程序
- 限制对特定页面和文件夹的访问
- 将文件上载到 web 应用程序
- 实施输入的验证
- 注册 web 应用程序路由
- 实现错误处理和错误日志记录

## <a name="overview"></a>概述

本教程系列是用于人熟悉的编程概念，但熟悉 ASP.NET Web 窗体。 如果您已经熟悉 ASP.NET Web 窗体，这一系列仍然可以帮助您了解新的 ASP.NET 4.5 功能。 有关读者熟悉编程概念和 ASP.NET Web 窗体，请参阅中提供的其他 Web 窗体教程[Getting Started](../../../index.md) ASP.NET 网站上的部分。

在本系列教程中提供在 ASP.NET 4.5 包括以下功能：

- 提供了用于创建项目的简单 UI[对许多 ASP.NET 框架的支持](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add)（Web 窗体、 MVC 和 Web API）。
- [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap)，布局、 主题和响应式设计框架。
- [ASP.NET 标识](../../../../identity/index.md)，新的 ASP.NET 成员资格系统的与 web 托管 IIS 之外的软件的工作方式在所有 ASP.NET 框架和工作原理相同。
- [Entity Framework 6](https://msdn.microsoft.com/data/ef.aspx)

  实体框架使您能够更新：
  - 检索和操作数据作为强类型化对象
  - 以异步方式访问数据
  - 处理暂时性连接故障
  - 日志的 SQL 语句

有关完整的 ASP.NET 4.5 功能列表，请参阅[ASP.NET 和 Web Tools for Visual Studio 2013 发行说明](../../../../visual-studio/overview/2013/release-notes.md)。

### <a name="the-wingtip-toys-sample-application"></a>Wingtip Toys 示例应用程序

下面的屏幕截图取自您在本系列教程中创建的 ASP.NET Web 窗体应用程序。 在 Visual Studio 中运行应用程序时，将显示以下 web 主页。

![Wingtip Toys 的默认页](introduction-and-overview/_static/image1.png)

可以将注册为新用户，或以现有用户身份登录。 顶部导航栏上的链接从数据库产品类别以及他们的产品。

如果选择**产品**，显示所有可用产品。 

![Wingtip Toys 的产品](introduction-and-overview/_static/image2.png)

如果选择特定产品时，将显示产品详细信息。


![Wingtip Toys 的产品详细信息](introduction-and-overview/_static/image3.png)

作为用户，可以注册和登录 Web 窗体模板的默认功能。 本教程还介绍如何使用现有的 Gmail 帐户登录。 此外，你可以以管理员身份登录来添加和从数据库中删除产品。

![Wingtip Toys-登录](introduction-and-overview/_static/image4.png)

你已以用户身份登录，你可以将产品添加到购物车并使用 PayPal 结帐。 示例应用程序用于处理 PayPal 的开发人员沙箱中。 没有实际资金事务会发生。

![Wingtip Toys 的购物车](introduction-and-overview/_static/image5.png)

PayPal 确认你的帐户、 订单和付款信息。

![Wingtip Toys - PayPal](introduction-and-overview/_static/image6.png)

返回从 PayPal 后, 可以查看并完成你的订单。

![Wingtip Toys 的顺序查看](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a>系统必备

在开始之前，请确保您的计算机上安装以下软件：

- [Microsoft Visual Studio 2017 或 Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/)。

会自动安装.NET Framework。

本系列教程使用 Microsoft Visual Studio Community 2017。 可以使用任一，或 Microsoft Visual Studio 2017，若要完成本系列教程。

请注意以下有关 Visual Studio:

* Microsoft Visual Studio 2017和Microsoft Visual Studio社区2017在本教程系列中称为*Visual Studio*。

* Visual Studio 2017 安装已安装任何较旧版本旁边。 在早期版本中创建的站点可以在 Visual Studio 2017 中打开，并继续在以前的版本中打开。

* 首次启动 Visual Studio 时，假定所选*Web 开发*设置。 有关详细信息，请参阅[如何：选择 Web 开发环境设置](https://msdn.microsoft.com/library/ff521558.aspx)。

在安装后系统必备组件，现在即可开始创建在本系列教程中提供的 Web 项目。

## <a name="download-the-sample-application"></a>下载示例应用程序

 您可以从 MSDN 示例网站随时下载在已完成的示例 applicatiion:

[开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2013-Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) 

 此下载具有以下各项：

- 中的示例应用程序*WingtipToys*文件夹。
- 若要创建示例应用程序所使用的资源*WingtipToys 资产*中的文件夹*WingtipToys*文件夹。

在下载 *.zip*文件。 若要查看已完成本教程系列中创建的项目，找到并选择*C#* 文件夹的.zip 文件。 保存C#使用用于 Visual Studio 项目的文件夹的文件夹。 默认情况下，Visual Studio 2017 项目文件夹为：

<strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong>

重命名***C#*** 向文件夹***WingtipToys***。

> [!NOTE]
> 如果已有一个名为文件夹*WingtipToys*在项目文件夹中，暂时重命名该现有文件夹重命名前*C#* 文件夹*WingtipToys*。

若要运行已完成的项目，打开*WingtipToys*文件夹，然后双击*WingtipToys.sln*文件。 Visual Studio 2017 打开项目。 接下来，右键单击*Default.aspx*中的文件**解决方案资源管理器**，然后选择**中浏览器中查看**。

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a>参加的 ASP.NET Web 窗体测验，若要查看内容

完成后的系列教程，请测试您的知识并加强关键概念小测验。 每个问题提供的说明和链接的其他指导。

 * [ASP.NET Web 窗体测验](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a>教程支持和提出的意见

有关的问题和意见，使用包含的问题与解答部分[开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2013-Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) 示例页面。

在本教程系列中的注释是欢迎使用。 更新本系列教程后，每项工作是进行更正或改进建议，请考虑。

如果发生错误，相应的错误消息可能是令人困惑，与不合理的解释如何修复此错误。 如需帮助，可以查看[ASP.NET 论坛](https://forums.asp.net/)。 另一个很好来源是中的问题与解答部分[开始使用 ASP.NET 4.5 Web 窗体和 Visual Studio 2013-Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) 示例页面。 

> [!div class="step-by-step"]
> [下一页](create-the-project.md)
