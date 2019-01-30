---
uid: identity/overview/getting-started/adding-aspnet-identity-to-an-empty-or-existing-web-forms-project
title: 添加 ASP.NET 标识设置为空的或现有 Web 窗体项目 |Microsoft Docs
author: raquelsa
description: 本教程演示如何将 ASP.NET 标识 （ASP.NET 的新成员资格系统） 添加到 ASP.NET 应用程序。 当您创建新的 Web 窗体或 MVC...
ms.author: riande
ms.date: 01/22/2019
ms.assetid: 1cbc0ed2-5bd6-4b62-8d34-4c193dcd8b25
msc.legacyurl: /identity/overview/getting-started/adding-aspnet-identity-to-an-empty-or-existing-web-forms-project
msc.type: authoredcontent
ms.openlocfilehash: cd28cc68db96b52eb205b8764aa2af014ffad9c3
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236505"
---
# <a name="adding-aspnet-identity-to-an-empty-or-existing-web-forms-project"></a>添加 ASP.NET 标识对空的或现有 Web 窗体项目


> 本教程演示如何添加[ASP.NET 标识](introduction-to-aspnet-identity.md)（用于 ASP.NET 的新成员资格系统） 的 ASP.NET 应用程序。
> 
> 当在 Visual Studio 2017 RTM 使用单独的帐户中创建新的 Web 窗体或 MVC 项目时，Visual Studio 会安装所需的所有包，并为您添加所有必需的类。 本教程将说明如何将 ASP.NET 身份验证支持添加到现有的 Web 窗体项目或新的空项目。 我将简要介绍所有您需要安装，NuGet 包和需要添加的类。 用于注册新的用户和日志记录在突出显示的用户管理和身份验证的所有主入口点 Api，我将绕过示例 Web 窗体。 此示例将使用 ASP.NET 标识默认实现，用于基于实体框架的 SQL 数据存储。 本教程中，我们将使用 LocalDB 的 SQL 数据库。
> 

## <a name="get-started-with-aspnet-identity"></a>开始使用 ASP.NET 标识

1. 首先，安装并运行[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。
2. 选择**新的项目**从一开始页上，或者可以使用菜单并选择**文件**，然后**新项目**。
3. 在左窗格中，展开**可视化C#** ，然后选择**Web**，然后**ASP.NET Web 应用程序 (.Net Framework)**。 为项目命名为"WebFormsIdentity"并选择**确定**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image17.png)
4. 在中**新建 ASP.NET 项目**对话框中，选择**空**模板。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image2.png)  
  
   请注意**更改身份验证**按钮禁用，并且在此模板中未提供任何身份验证支持。 Web 窗体、 MVC 和 Web API 模板允许您选择的身份验证方法。

## <a name="add-identity-packages-to-your-app"></a>将标识包添加到您的应用程序

在解决方案资源管理器，右键单击项目并选择**管理 NuGet 包**。 搜索并安装**Microsoft.AspNet.Identity.EntityFramework**包。 
  
![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image15.png)
  
请注意，此包将安装依赖项包：**EntityFramework**并**Microsoft ASP.NET 标识核心**。

## <a name="add-a-web-form-to-register-users"></a>添加 web 窗体为用户注册

1. 在中**解决方案资源管理器**，右键单击项目并选择**添加**，然后**Web 窗体**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image4.png)
2. 在中**指定项名称**对话框中，将新的 web 窗体命名**注册**，然后选择**确定**
3. 将在生成标记为*Register.aspx*下面的代码文件。 代码所作更改为突出显示状态。 

    [!code-html[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample1.aspx?highlight=9,12-40)]

    > [!NOTE]
    > 这是只是一个简化的版的*Register.aspx*时创建新的 ASP.NET Web 窗体项目创建的文件。 上面的标记添加窗体字段和一个按钮，用于注册新的用户。
4. 打开*Register.aspx.cs*文件和文件的内容替换为以下代码：

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample2.cs)]

    > [!NOTE] 
    > 
    > 1. 上面的代码是一个简化的版的*Register.aspx.cs*时创建新的 ASP.NET Web 窗体项目创建的文件。
    > 2. *IdentityUser*类是默认的 EntityFramework 实现*IUser*接口。 *IUser*接口是在 ASP.NET Core 标识用户的最小接口。
    > 3. *UserStore*类是默认的 EntityFramework 实现的用户存储区。 此类实现 ASP.NET 标识 Core 的最少的接口：*IUserStore*， *IUserLoginStore*， *IUserClaimStore*并*IUserRoleStore*。
    > 4. *UserManager*类公开与用户相关 Api 会自动将更改保存*UserStore*。
    > 5. *IdentityResult*类表示标识操作的结果。
5. 在中**解决方案资源管理器**，右键单击项目并选择**添加**，**添加 ASP.NET 文件夹**，然后**应用\_数据**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image5.png)
6. 打开*Web.config*文件，并添加我们将使用它来存储用户信息的数据库的连接字符串项。 数据库将创建在运行时通过 EntityFramework 的标识实体。 连接字符串是类似于创建新的 Web 窗体项目时，将创建一个。 突出显示的代码演示了应添加的标记：

    [!code-xml[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample3.xml?highlight=11-14)]
    
    > [!NOTE] 
    > 用于 Visual Studio 2015 或更高版本，替换`(localdb)\v11.0`与`(localdb)\MSSQLLocalDB`在连接字符串中。
    
7. 右键单击文件*Register.aspx*项目，选择在**设为起始页**。 按 Ctrl + F5 生成并运行 web 应用程序。 输入新用户名和密码，然后选择**注册**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image6.png)  

    > [!NOTE]
    > ASP.NET 标识具有对验证的支持，在此示例中，你可以验证用户和密码的默认行为来自标识核心包的验证程序。 用户的默认验证程序 (`UserValidator`) 都有一个属性`AllowOnlyAlphanumericUserNames`，其默认值设置为`true`。 密码的默认验证程序 (`MinimumLengthValidator`) 可确保该密码包含至少 6 个字符。 这些验证程序属性位于`UserManager`，如果你想要具有自定义验证，可以重写

## <a name="verify-the-localdb-identity-database-and-tables-generated-by-entity-framework"></a>验证 LocalDb 标识数据库和实体框架所生成的表

1. 在中**视图**菜单中，选择**服务器资源管理器**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image7.png)
2. 展开**DefaultConnection (WebFormsIdentity)**，展开**表**，右键单击**AspNetUsers** ，然后选择**显示表数据**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image8.png)  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image9.png)

## <a name="configure-the-application-for-owin-authentication"></a>配置应用程序的 OWIN 身份验证

在这里我们只添加了支持用于创建用户。 现在，我们要演示如何添加身份验证登录用户。 ASP.NET 标识窗体身份验证使用 Microsoft OWIN 身份验证中间件。 OWIN Cookie 身份验证 cookie 和声明可由任何框架上托管的基于身份验证机制[OWIN](https://msdn.microsoft.com/magazine/dn451439.aspx)或 IIS。 使用此模型中，可以跨多个框架，包括 ASP.NET MVC 和 Web 窗体使用相同的身份验证包。 有关详细信息 Katana 项目以及如何在主机不可知请运行中间件[Katana 项目入门](https://msdn.microsoft.com/magazine/dn451439.aspx)。

## <a name="install-authentication-packages-to-your-application"></a>身份验证包安装到你的应用程序

1. 在解决方案资源管理器，右键单击项目并选择**管理 NuGet 包**。 搜索并安装***Microsoft.AspNet.Identity.Owin***包。 
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image16.png)

2. 搜索并安装***Microsoft.Owin.Host.SystemWeb***包。

    > [!NOTE]
    > **Microsoft.Aspnet.Identity.Owin**包中包含一系列 OWIN 扩展类，用于管理和配置 OWIN 身份验证中间件可供 ASP.NET 标识 Core 包。
    > **Microsoft.Owin.Host.SystemWeb**包包含可使基于 OWIN 的应用程序使用 ASP.NET 请求管道在 IIS 上运行的 OWIN 服务器。 有关详细信息请参阅[在 IIS 中的 OWIN 中间件集成管道](../../../aspnet/overview/owin-and-katana/owin-middleware-in-the-iis-integrated-pipeline.md)。

## <a name="add-owin-startup-and-authentication-configuration-classes"></a>添加 OWIN 启动和身份验证配置类

1. 在中**解决方案资源管理器**，右键单击项目，选择**添加**，然后**添加新项**。 在搜索文本框对话框，键入"*owin*"。 类命名为"*启动*"，然后选择**添加**。 
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image11.png)
2. 在 Startup.cs 文件中，添加突出显示的代码如下所示配置 OWIN cookie 身份验证。

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample4.cs?highlight=1,3,15-19)]

    > [!NOTE]
    > 此类包含`OwinStartup`指定 OWIN 启动类的属性。 每个 OWIN 应用程序有一个 startup 类在其中指定的应用程序管道组件。 请参阅[OWIN 启动类检测](../../../aspnet/overview/owin-and-katana/owin-startup-class-detection.md)有关此模型的详细信息。

## <a name="add-web-forms-for-registering-and-signing-in-users"></a>添加注册和登录用户的 web 窗体

1. 打开*Register.aspx.cs*文件，并添加下面的代码中用户的登录时注册成功。

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample5.cs)]

    > [!NOTE] 
    > 
    > - 由于 ASP.NET 标识和 OWIN Cookie 身份验证是基于声明的系统，因此，框架需要应用开发人员生成[ClaimsIdentity](https://msdn.microsoft.com/library/microsoft.identitymodel.claims.claimsidentity.aspx)用户。 ClaimsIdentity 具有信息如用户所属的角色的用户的所有声明。 在此阶段，还可以添加更多的用户声明。
    > - 可以在用户登录通过使用从 OWIN 和调用 AuthenticationManager`SignIn`并传入 ClaimsIdentity 如上所示。 此代码将在用户登录，并生成以及一个 cookie。 此调用是类似于[FormAuthentication.SetAuthCookie](https://msdn.microsoft.com/library/system.web.security.formsauthentication.setauthcookie.aspx)由[FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx)模块。
2. 在中**解决方案资源管理器**，右键单击项目，选择**添加**，然后**Web 窗体**。 Web 窗体命名为**登录名**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image12.png)
3. 内容替换为*Login.aspx*文件使用以下代码：

    [!code-aspx[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample6.aspx)]
4. 内容替换为*Login.aspx.cs*具有以下文件：

    [!code-csharp[Main](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/samples/sample7.cs)]

    > [!NOTE] 
    > 
    > - `Page_Load`现在检查当前用户的状态并采取操作基于其`Context.User.Identity.IsAuthenticated`状态。
    >     **显示已登录用户名**:Microsoft ASP.NET 标识框架上添加了扩展方法[System.Security.Principal.IIdentity](https://msdn.microsoft.com/library/system.security.principal.iidentity.aspx) ，可用于获取`UserName`和`UserId`中用户已登录。 这些扩展方法定义中`Microsoft.AspNet.Identity.Core`程序集。 这些扩展方法是将用于[HttpContext.User.Identity.Name](https://msdn.microsoft.com/library/system.web.httpcontext.user.aspx) 。
    > - 登录方法：`This`方法将替换以前`CreateUser_Click`中此示例和现在中成功创建用户后用户登录方法。   
    >   Microsoft OWIN 框架上添加了扩展方法`System.Web.HttpContext`，可用于获取对引用`IOwinContext`。 这些扩展方法定义中`Microsoft.Owin.Host.SystemWeb`程序集。 `OwinContext`类公开`IAuthenticationManager`属性表示可在当前请求上的身份验证中间件功能。 可以通过使用来在用户登录`AuthenticationManager`从 OWIN 和调用`SignIn`并传入`ClaimsIdentity`如上所示。 ASP.NET 标识和 OWIN Cookie 身份验证是基于声明的系统，因为框架需要应用程序以生成`ClaimsIdentity`用户。 `ClaimsIdentity`具有信息的用户，如用户所属的角色的所有声明。 此外可以在此阶段，此代码将在用户登录，并生成一个 cookie 也添加更多的用户声明。 此调用是类似于[FormAuthentication.SetAuthCookie](https://msdn.microsoft.com/library/system.web.security.formsauthentication.setauthcookie.aspx)由[FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx)模块。
    > - `SignOut` 方法：获取对`AuthenticationManager`从 OWIN 和调用`SignOut`。 这是类似于[FormsAuthentication.SignOut](https://msdn.microsoft.com/library/system.web.security.formsauthentication.signout.aspx)方法，由[FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx)模块。
5. 按**Ctrl + F5**生成并运行 web 应用程序。 输入新用户名和密码，然后选择**注册**。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image13.png)  
   注意:此时，新用户创建并登录。
6. 选择**注销**按钮。 已重定向到登录窗体。
7. 输入了无效的用户名或密码，然后选择**登录**按钮。 
   `UserManager.Find`方法将返回空值和错误消息："*无效的用户名或密码*"将显示。
  
    ![](adding-aspnet-identity-to-an-empty-or-existing-web-forms-project/_static/image14.png)
