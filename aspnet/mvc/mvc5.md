---
uid: mvc/mvc5
title: ASP.NET MVC 5 | Microsoft Docs
author: rick-anderson
description: ASP.NET MVC 5 ASP.NET MVC 5 是一个框架，用于构建可缩放的基于标准的 web 应用程序使用成熟设计模式以及强大的 AS....
ms.author: riande
ms.date: 10/11/2018
ms.assetid: f79fbf7f-59e5-4279-a832-c1a0294630f4
msc.legacyurl: /mvc/mvc5
msc.type: content
ms.openlocfilehash: 14fcf863a4ef5f6c9180cdf9e7b632ccdb1ebcb0
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410464"
---
<a name="aspnet-mvc-5"></a>ASP.NET MVC 5
====================
## <a name="whats-new-in-aspnet-mvc-5"></a>什么是 ASP.NET MVC 5 中的新增功能

### <a name="one-aspnet"></a>One ASP.NET

Web MVC 项目模板与 One ASP.NET 体验无缝集成。 您可以自定义 MVC 项目和配置身份验证使用 One ASP.NET 项目创建向导。 为 ASP.NET MVC 5 的介绍性教程，请参阅[Getting Started with ASP.NET MVC 5](overview/getting-started/introduction/getting-started.md)。

有关将 MVC 4 项目升级到 MVC 5 的信息，请参阅[如何将 ASP.NET MVC 4 和 Web API 项目升级到 ASP.NET MVC 5 和 Web API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md)。

### <a name="aspnet-identity"></a>ASP.NET 标识

已更新的 MVC 项目模板来使用 ASP.NET 标识进行身份验证和标识管理。 提供 Facebook 和 Google 身份验证和新的成员资格 API 的教程，请参阅[创建 ASP.NET MVC 5 应用程序使用 Facebook 和 Google OAuth2 和 OpenID 登录](overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)和[部署包含的安全 ASP.NET MVC 应用成员资格、 OAuth 和 SQL 数据库添加到 Windows Azure 网站](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)。

### <a name="bootstrap"></a>Bootstrap

MVC 项目模板已更新为使用[Bootstrap](http://getbootstrap.com/)提供时尚且响应迅速的界面外观，可以轻松地自定义。 有关详细信息，请参阅[Visual Studio web 项目模板中的 Bootstrap](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap)。

### <a name="authentication-filters"></a>身份验证筛选器

[身份验证筛选器](http://www.dotnetcurry.com/showarticle.aspx?ID=957)是一种新的筛选器在 ASP.NET MVC 中，在 ASP.NET MVC 管道中的授权筛选器之前运行，并允许您指定身份验证逻辑每个操作，每个控制器或全局范围内的所有控制器。 身份验证筛选器处理在请求中的凭据，并提供相应的用户权限。 身份验证筛选器还可以在未经授权的请求响应中添加身份验证质询。 请参阅[ASP.NET MVC 5 身份验证筛选器](http://www.dotnetcurry.com/showarticle.aspx?ID=957)， [ASP.NET MVC 5 中的身份验证筛选器](http://theshravan.net/blog/authentication-filters-in-asp-net-mvc-5/)。

### <a name="filter-overrides"></a>筛选器替代项

你现在可以重写的筛选器适用于给定的操作方法或控制器通过指定[重写筛选器](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5)。 覆盖筛选器指定一的组不应运行给定作用域 （操作或控制器） 的筛选器类型。 这允许你配置的全局应用，但然后排除特定全局筛选器应用到特定操作或控制器筛选器。 请参阅[ASP.NET MVC 5 和 ASP.NET Web API 2 中的新筛选器替代项新功能](https://weblogs.asp.net/imranbaloch/archive/2013/09/25/new-filter-overrides-in-asp-net-mvc-5-and-asp-net-web-api-2.aspx)，[如何使用 ASP.NET MVC 5 筛选器将重写功能](http://hackwebwith.net/how-to-use-the-asp-net-mvc-5-filter-overrides-feature/)，和[ASP.NET MVC 5 中的筛选器替代项](http://www.davidhayden.me/blog/filter-overrides-in-asp-net-mvc-5)

### <a name="attribute-routing"></a>属性路由

ASP.NET MVC 现在支持[的属性路由](https://blogs.msdn.com/b/webdev/archive/2013/10/17/attribute-routing-in-asp-net-mvc-5.aspx)，得益于由 Tim McCall，一书的作者的发布内容[AttributeRouting](https://github.com/mccalltd/AttributeRouting)。 使用属性路由可以对操作和控制器进行批注来指定路由。

## <a name="new-web-project-experience"></a>新的 Web 项目体验

Visual Studio 增强启动 Visual Studio 2013 中创建新的 web 项目的体验。 在中**新的 ASP.NET Web 项目**对话框，可以选择希望、 配置技术 (Web 窗体、 MVC、 Web API) 的任意组合、 配置身份验证选项，添加 Docker 支持和添加单元测试项目的项目类型。

![新建 ASP.NET 项目](mvc5/_static/new-aspnet-web-app-dialog.png)

该对话框可以更改许多模板的默认身份验证选项。 例如，创建 ASP.NET Web 窗体项目时您可以选择以下选项之一：

- 无身份验证
- 单个用户帐户 （ASP.NET 成员身份或社交提供程序登录）
- 工作或学校帐户 (Active Directory 中的 internet 应用程序)
- Windows 身份验证 (Active Directory 中的 intranet 应用程序)

![身份验证选项](mvc5/_static/change-authentication-dialog.png)

有关创建 web 项目的过程的详细信息，请参阅[在 Visual Studio 中创建 ASP.NET Web 项目](../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md)。 有关身份验证选项的详细信息，请参阅[ASP.NET 标识](../identity/overview/index.md)。

<a id="scaffold"></a>
### <a name="aspnet-scaffolding"></a>ASP.NET 基架

ASP.NET 基架是用于 ASP.NET Web 应用程序的代码生成框架。 它可以轻松地将样板代码添加到项目中使用的数据模型进行交互。

在 Visual studio 2013 之前的版本，基架被限制为 ASP.NET MVC 项目。 从 Visual Studio 2013 开始，可以使用有关任何 ASP.NET 项目，包括 Web 窗体基架。 Visual Studio 目前不支持生成页对于 Web 窗体项目，但您仍可以使用基架使用 Web 窗体通过向项目添加 MVC 依赖项。 未来版本中，将添加对生成的 Web 窗体页的支持。

在使用基架时，所有必需的依赖项安装在项目中。 例如，如果您开始创建 ASP.NET Web 窗体项目，然后使用基架添加 Web API 控制器，所需的 NuGet 包和引用将自动添加到你的项目。

若要将 MVC 基架添加到 Web 窗体项目，添加**新基架项**，然后选择**MVC 5 依赖项**在对话框窗口中。 有两个选项基架 MVC;**最小依赖项**并**完全依赖项**。 如果选择**最小依赖项**，NuGet 包和 ASP.NET MVC 的引用添加到你的项目。 如果选择**完整的依赖项**，最小依赖项添加以及 MVC 项目所需内容文件。

![在 Visual Studio 中添加基架对话框](overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/add-scaffold.png)

基架异步控制器支持使用从 Entity Framework 6 的异步功能。

有关详细信息和教程，请参阅[ASP.NET 基架概述](../visual-studio/overview/2013/aspnet-scaffolding-overview.md)。

### <a name="get-help-and-report-issues"></a>获取帮助和报表问题

- [已知的问题和重大更改列表](../visual-studio/overview/2013/release-notes.md#knownissues)
- 获取帮助和讨论中的 ASP.NET MVC 5[论坛](https://forums.asp.net/1146.aspx)
- [报告在 ASP.NET MVC 5 中的 bug](https://github.com/aspnet/AspNetWebStack/issues)
- [提出功能请求](http://aspnet.uservoice.com/forums/41201-asp-net-mvc)

### <a name="upgrade-from-aspnet-mvc-4"></a>从 ASP.NET MVC 4 升级

请参阅[如何升级 ASP.NET MVC 4 和 Web API 项目到 ASP.NET MVC 5 和 Web API 2](overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2.md)
