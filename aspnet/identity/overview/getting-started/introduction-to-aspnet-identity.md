---
uid: identity/overview/getting-started/introduction-to-aspnet-identity
title: ASP.NET 标识简介 |Microsoft Docs
author: jongalloway
description: ASP.NET 成员资格系统引入了 ASP.NET 2.0 后在 2005 中，并且发生了很多更改的方式的 web 应用程序内，然后...
ms.author: riande
ms.date: 01/22/2019
ms.assetid: 38717fc1-5989-43cf-952d-4007cc1dd923
msc.legacyurl: /identity/overview/getting-started/introduction-to-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 4a545e52d2d9ea04a10c37c116fd326c60de9f8f
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236440"
---
<a name="introduction-to-aspnet-identity"></a>ASP.NET 标识简介
====================

> ASP.NET 成员资格系统引入了 ASP.NET 2.0 后并且在 2005 年，则发生了很多更改 web 应用程序通常处理身份验证和授权的方式。 ASP.NET 标识是在生成适用于 web、 手机或平板电脑的现代应用程序时，成员资格系统应为新的外观。


## <a name="background-membership-in-aspnet"></a>背景：在 ASP.NET 中的成员身份

### <a name="aspnet-membership"></a>ASP.NET 成员资格

[ASP.NET 成员资格](https://msdn.microsoft.com/library/yh26yfzy(v=VS.100).aspx)旨在解决常见的 2005 年，涉及到窗体身份验证和 SQL Server 数据库的用户名、 密码和配置文件数据的站点成员身份要求。 目前没有更广泛的任务的 web 应用程序的数据存储选项，大多数开发人员想要启用其站点以使用社交标识提供者进行身份验证和授权功能。 ASP.NET 成员资格的设计的限制使这一转换困难：

- 适用于 SQL Server 设计数据库架构，而且不能更改它。 您可以添加配置文件信息，但其他数据打包到不同的表，这便很难访问通过除配置文件提供程序 API 通过任何方式。
- 提供程序系统，您可以更改后备数据存储，但系统围绕假设适用于关系数据库设计的。 您可以编写一个提供程序来存储非关系存储机制，例如 Azure 存储表中的成员身份信息但则必须通过编写大量代码和很多关系的设计来解决`System.NotImplementedException`不的方法的异常适用于 NoSQL 数据库。
- 由于日志/日志扩展功能基于窗体身份验证，不能使用成员资格系统[OWIN](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)。 OWIN 包括中间件组件进行身份验证，包括对登录名使用外部标识提供者 （如 Microsoft 帐户、 Facebook、 Google、 Twitter)、 支持和登录名使用组织帐户从本地 Active Directory 或Azure Active Directory。 OWIN 还包括对 OAuth 2.0 JWT，CORS 支持。

### <a name="aspnet-simple-membership"></a>ASP.NET 简单成员资格

[ASP.NET 简单成员资格](../../../web-pages/overview/security/16-adding-security-and-membership.md)作为开发成员资格系统为 ASP.NET Web Pages。 它随 WebMatrix 和 Visual Studio 2010 SP1 一起发布。 简单成员资格的目标是轻松地将成员资格功能添加到 Web Pages 应用程序。

简单成员资格确实能够更轻松地自定义用户配置文件信息，但它仍共享 ASP.NET 成员资格的其他问题，它具有某些限制：

- 会很难保持非关系存储区中的成员资格系统数据。
- 您不能将其与 OWIN。
- 它不太适合现有 ASP.NET 成员资格提供程序，并且不可扩展。

### <a name="aspnet-universal-providers"></a>ASP.NET 通用提供程序

[ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx)不断问世以使其能够进行保持 Azure SQL 数据库，并且它们也可使用 SQL Server Compact 中 Microsoft 的成员身份信息。 Entity Framework Code First，这意味着可以使用通用提供程序来保持 EF 支持任何存储区中的数据生成通用提供程序。 使用通用提供程序，很多也清除数据库架构。

通用提供程序是基于 ASP.NET 成员资格的基础结构，因此它们仍然执行相同的限制为 SqlMembership 提供程序。 也就是说，它们旨在用于关系数据库和很难自定义配置文件和用户信息。 这些提供程序也仍使用窗体身份验证的登录和注销功能。

## <a name="aspnet-identity"></a>ASP.NET 标识

作为成员资格情景在 ASP.NET 中的发展多年来，ASP.NET 团队已积累了许多经验客户的反馈。

通过输入用户名和密码，它们具有自己的应用程序中注册登录用户，将假设将不再有效。 Web 变得更多社交。 用户通过 Facebook、 Twitter 和其他社交网站等社交渠道实时与彼此交互。 开发人员希望用户能够使用其社交标识登录，以便他们可以在自己网站上有丰富的体验。 现代的成员身份系统必须启用到身份验证提供程序，如 Facebook、 Twitter 和其他基于重定向的日志项。

随着 web 开发的发展，因此做了 web 开发的模式。 单元测试的应用程序代码成为核心需考虑的应用程序开发人员。 在 2008 年 ASP.NET 添加一个新的框架基于模型-视图-控制器 (MVC) 模式，部分可帮助开发人员构建可测试的 ASP.NET 应用程序的单元。 开发人员希望单元测试其应用程序逻辑，还希望能够使用成员资格系统执行该操作。

考虑到 web 应用程序开发中的这些更改，ASP.NET 标识被开发与以下目标：

- **一个 ASP.NET 标识系统**

    - ASP.NET 标识可用于所有 ASP.NET 框架，如 ASP.NET MVC、 Web 窗体、 网页、 Web API 和 SignalR。
    - 在生成 web、 电话、 存储或混合应用程序时，可以使用 ASP.NET 标识。
- **易用性插入有关用户的配置文件数据**

    - 你可以控制用户配置文件信息的架构。 例如，您可以轻松实现系统来存储应用程序中注册帐户时，用户输入的出生日期。

- **持久性控件**

    - 默认情况下，ASP.NET 标识系统在数据库中存储所有用户信息。 ASP.NET 标识使用 Entity Framework Code First 来实现所有持久性机制。
    - 由于您控制的数据库架构、 常见任务，例如更改表名称或更改主键的数据类型很简单。
    - 很容易地插入不同的存储机制，例如 SharePoint、 Azure 存储表服务、 NoSQL 数据库等，而无需引发`System.NotImplementedExceptions`异常。
- **单元测试**

    - ASP.NET 标识，可以在 web 应用程序多个单元可测试性。 您可以编写单元测试使用 ASP.NET 标识的应用程序各部分。
- **角色提供程序**

    - 没有可让你通过角色限制对应用程序的部分访问的角色提供程序。 您可以轻松地创建角色，如"Admin"和向角色添加用户。
- **基于声明**

    - ASP.NET 标识支持基于声明的身份验证，其中用户的标识都表示为一组声明。 声明时，允许开发人员在描述用户的标识不是角色允许更有意义。 角色成员身份是只是一个布尔值 （成员或非成员），而声明可以包括有关用户的标识和成员身份的丰富信息。
- **社交登录提供程序**

    - 可以轻松地添加到应用程序，如 Microsoft 帐户、 Facebook、 Twitter、 Google 和其他社交登录并在你的应用程序中存储特定于用户的数据。

- **OWIN 集成**

    - ASP.NET 身份验证现在基于可在任何基于 OWIN 的主机使用的 OWIN 中间件。 ASP.NET 标识 System.Web 没有任何依赖项。 它是一个完全符合 OWIN 框架，并可在任何托管的 OWIN 应用程序。
    - ASP.NET 标识的日志-在/注销的 web 站点中的用户使用 OWIN 身份验证。 这意味着，而不是使用 FormsAuthentication 生成 cookie，应用程序使用 OWIN CookieAuthentication 来执行该操作。
- **NuGet 包**

    - ASP.NET 标识是以 ASP.NET MVC、 Web 窗体和 Web API 模板随附于 Visual Studio 2017 中安装 NuGet 包的形式重新分发。 可以从 NuGet 库下载此 NuGet 包。
    - 发布 ASP.NET 标识为 NuGet 程序包使得 ASP.NET 团队来循环访问新功能和 bug 修复和敏捷的方式提供给开发人员更轻松。

## <a name="get-started-with-aspnet-identity"></a>开始使用 ASP.NET 标识

在 Visual Studio 2017 项目模板中使用 ASP.NET 标识为 ASP.NET MVC、 Web 窗体、 Web API 和 SPA。 在此演练中，我们将演示项目模板如何使用 ASP.NET 标识来添加注册、 登录和注销用户的功能。

ASP.NET 标识是使用以下过程来实现的。 本文的目的是为您提供的 ASP.NET 标识; 高级别概述可以按照其步骤或只需读取的详细信息。 有关更详细的说明创建应用程序使用 ASP.NET 标识，包括使用新的 API 来添加用户、 角色和配置文件信息，请参阅本文末尾的后续步骤部分。

1. 使用个人帐户创建一个 ASP.NET MVC 应用程序。 可以使用 ASP.NET MVC、 Web 窗体、 Web API、 SignalR 等中的 ASP.NET 标识。在本文中，我们将开始使用 ASP.NET MVC 应用程序。  
  
    ![](introduction-to-aspnet-identity/_static/image1.png)
2. 创建的项目的 ASP.NET 标识包含以下三个包。

    - [`Microsoft.AspNet.Identity.EntityFramework`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.EntityFramework/)  
   此包具有的 ASP.NET 标识数据和架构到 SQL Server 将保存 ASP.NET 标识的实体框架实现。
    - [`Microsoft.AspNet.Identity.Core`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Core/)  
   此包具有 ASP.NET Identity 的核心接口。 此包可用于编写，目标不同的持久性存储如 Azure 表存储，NoSQL 数据库等 ASP.NET Identity 的实现。
    - [`Microsoft.AspNet.Identity.OWIN`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Owin/)  
   此包包含用于在 ASP.NET 应用程序中使用 ASP.NET 标识插入 OWIN 身份验证中的功能。 这用于登录的功能添加到你的应用程序和调入 OWIN Cookie 身份验证中间件来生成 cookie 时。
3. 创建用户。  
   启动应用程序，然后单击**注册**链接创建的用户。 下图显示了注册页，以收集用户名和密码。  
  
    ![](introduction-to-aspnet-identity/_static/image2.png)  
  
   当用户选择**注册**按钮，`Register`在 Account 控制器的操作通过调用 ASP.NET 标识 API，为以下突出显示部分创建用户：

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample1.cs?highlight=8-9)]
4. 登录。  
   如果已成功创建用户，她由签名`SignInAsync`方法。  

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample6.cs?highlight=12)]


   `SignInManager.SignInAsync`方法将生成[ClaimsIdentity](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx)。 由于 ASP.NET 标识和 OWIN Cookie 身份验证是基于声明的系统，该框架要求应用以生成用户的 ClaimsIdentity。 ClaimsIdentity 具有的用户，如用户所属的角色的所有声明的信息。   
 
5. 注销。  
   选择**注销**链接以在 account 控制器中调用注销操作。 

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample5.cs?highlight=6)]

   突出显示的代码所示 OWIN`AuthenticationManager.SignOut`方法。 这是类似于[FormsAuthentication.SignOut](https://msdn.microsoft.com/library/system.web.security.formsauthentication.signout.aspx)方法，由[FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx) Web 窗体中的模块。

## <a name="components-of-aspnet-identity"></a>ASP.NET 标识的组件

下图显示了 ASP.NET 标识系统的组件 (上选择[这](introduction-to-aspnet-identity/_static/image3.png)或关系图进行放大)。 以绿色包构成了 ASP.NET 标识系统。 所有其他包都需要 ASP.NET 应用程序中使用 ASP.NET 标识系统的依赖项。

[![](introduction-to-aspnet-identity/_static/image5.png)](introduction-to-aspnet-identity/_static/image4.png)

下面是不前面提到的 NuGet 包的简短说明：

- [Microsoft.Owin.Security.Cookies](http://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/)  
 可让应用程序使用 cookie 中间件基于身份验证，类似于 ASP。NET 的窗体身份验证。
- [EntityFramework](http://www.nuget.org/packages/EntityFramework/)  
 Entity Framework 是 Microsoft 的建议的数据访问技术，用于关系数据库。

## <a name="migrating-from-membership-to-aspnet-identity"></a>从成员身份迁移到 ASP.NET 标识

我们希望即将迁移到新的 ASP.NET 标识系统中使用 ASP.NET 成员资格或简单成员资格的现有应用程序提供指导。

## <a name="next-steps"></a>后续步骤

- [使用 Facebook 和 Google 的 OAuth2 和 OpenID 登录创建 ASP.NET MVC 5 应用](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)  
 本教程使用 ASP.NET 标识 API 添加到用户数据库的配置文件信息以及如何使用 Google 和 Facebook 进行身份验证。
- [使用身份验证和 SQL 数据库创建 ASP.NET MVC 应用并将其部署到 Azure 应用服务](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)  
 本教程演示如何使用标识 API 来添加用户和角色。
- [https://github.com/rustd/AspnetIdentitySample](https://github.com/rustd/AspnetIdentitySample)  
 示例演示如何添加基本的角色和用户支持以及如何执行角色和用户管理的应用程序。
