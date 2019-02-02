---
uid: web-api/overview/security/external-authentication-services
title: 使用 ASP.NET Web API 的外部身份验证服务 (C#) |Microsoft Docs
author: rmcmurray
description: 介绍如何在 ASP.NET Web API 中使用外部身份验证服务。
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 3bb8eb15-b518-44f5-a67d-a27e051aedc6
msc.legacyurl: /web-api/overview/security/external-authentication-services
msc.type: authoredcontent
ms.openlocfilehash: de9b64e6c582059ec66ab352f60773f50af7b1ff
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667851"
---
# <a name="external-authentication-services-with-aspnet-web-api-c"></a>使用 ASP.NET Web API 的外部身份验证服务 (C#)

Visual Studio 2017 和 ASP.NET 4.7.2 展开的安全选项[单页面应用程序](../../../single-page-application/index.md)(SPA) 和[Web API](../../index.md)服务与外部身份验证服务，包含多个集成OAuth/OpenID 和社交身份验证服务：Microsoft 帐户、 Twitter、 Facebook 和 Google。  

### <a name="in-this-walkthrough"></a>在本演练中

- [使用外部身份验证服务](#USING)
- [创建示例 Web 应用程序](#SAMPLE)
- [启用 Facebook 身份验证](#FACEBOOK)
- [启用 Google 身份验证](#GOOGLE)
- [启用 Microsoft 身份验证](#MICROSOFT)
- [启用 Twitter 身份验证](#TWITTER)
- [其他信息](#MOREINFO)

    - [合并外部身份验证服务](#COMBINE)
    - [配置 IIS Express 以使用完全限定的域名](#FQDN)
    - [如何获取 Microsoft 身份验证应用程序设置](#OBTAIN)
    - [可选：禁用本地注册](#DISABLE)

### <a name="prerequisites"></a>系统必备

若要按照本演练中的示例，需要具备以下：

- Visual Studio 2017
- 具有应用程序标识符和一个以下社交媒体身份验证服务的机密密钥的开发人员帐户：

  - Microsoft 帐户 ([https://go.microsoft.com/fwlink/?LinkID=144070](https://go.microsoft.com/fwlink/?LinkID=144070))
  - Twitter ([https://dev.twitter.com/](https://dev.twitter.com/))
  - Facebook ([https://developers.facebook.com/](https://developers.facebook.com/))
  - Google ([https://developers.google.com/](https://developers.google.com))

<a id="USING"></a>
## <a name="using-external-authentication-services"></a>使用外部身份验证服务

丰富的 web 开发人员帮助以减少开发到当前可用的外部身份验证服务时创建新的 web 应用程序的时间。 Web 用户通常具有多个现有帐户用于流行的 web 服务和社交媒体网站，因此时身份验证的服务从外部 web 服务或社交媒体网站 web 应用程序实现，从而节省了开发时间，将已耗费创建身份验证实现。 使用外部身份验证服务将保存最终用户无需创建 web 应用程序，另一个帐户以及无需记住另一个用户名和密码。

在过去，开发人员已有两个选择： 创建自己的身份验证实现，或了解如何将外部身份验证服务集成到其应用程序。 在最基本的级别下, 图说明一个简单的请求流用户代理 （web 浏览器） 配置为使用外部身份验证服务的 web 应用程序请求的信息：

[![](external-authentication-services/_static/image2.png "单击此项可展开图像")](external-authentication-services/_static/image1.png)

在上图中，用户代理 （或在此示例中的 web 浏览器） 向发出请求的 web 应用程序，web 浏览器重定向到外部身份验证服务。 用户代理将其凭据发送到外部身份验证服务和外部身份验证服务的用户代理已成功通过身份验证，如果将重用户代理定向到使用某种形式的原始 web 应用程序的令牌用户代理将发送到 web 应用程序。 Web 应用程序将使用该令牌来验证用户代理已成功验证外部身份验证服务和 web 应用程序可能会使用该标记来收集有关用户代理的详细信息。 完成应用程序处理用户代理的信息，web 应用程序将返回到用户代理基于其授权设置适当的响应。

在此第二个示例中，与 web 应用程序和外部授权服务器协商用户代理和 web 应用程序执行外部授权服务器来检索有关用户的其他信息与其他通信代理：

[![](external-authentication-services/_static/image4.png "单击此项可展开图像")](external-authentication-services/_static/image3.png)

Visual Studio 2017 和 ASP.NET 4.7.2 与外部身份验证服务的集成更轻松地进行开发人员通过提供以下身份验证服务的内置集成：

- Facebook
- Google
- Microsoft 帐户 （Windows Live ID 帐户）
- Twitter

在本演练中的示例将演示如何使用 Visual Studio 2017 使用随附的新 ASP.NET Web 应用程序模板配置的每个受支持的外部身份验证服务。

> [!NOTE]
> 如有必要，您可能需要将你的 FQDN 添加到外部身份验证服务的设置。 此要求基于安全约束，某些外部身份验证服务，这需要你的应用程序设置中的 FQDN，以匹配你的客户端使用的 FQDN。 （此步骤将大不相同的每个外部身份验证服务; 你将需要参考的文档以查看这是否是必须每个外部身份验证服务以及如何配置这些设置。）如果你需要配置 IIS Express 以用于测试此环境中使用 FQDN，请参阅[配置 IIS Express 以使用完全限定的域名](#FQDN)本演练后面的部分。


<a id="SAMPLE"></a>
## <a name="create-a-sample-web-application"></a>创建示例 Web 应用程序

以下步骤将引导用户创建的示例应用程序使用 ASP.NET Web 应用程序模板，并将为每个外部身份验证服务稍后在本演练使用此示例应用程序。

启动 Visual Studio 2017，然后选择**新的项目**从起始页。 或者，从**文件**菜单中，选择**新建**，然后**项目**。

<!-- [![](external-authentication-services/_static/image6.png "Click to Expand the Image")](external-authentication-services/_static/image5.png) -->

当**新的项目**对话框中，选择**已安装**展开**Visual C#** 。 下**Visual C#**，选择**Web**。 在项目模板列表中选择**ASP.NET Web 应用程序 (.Net Framework)**。 输入你的项目的名称，然后单击**确定**。

[![](external-authentication-services/_static/image71.png "单击此项可展开图像")](external-authentication-services/_static/image71.png)

当**新建 ASP.NET 项目**的显示，请选择**单页面应用程序**模板，然后单击**创建项目**。

[![](external-authentication-services/_static/image72.png "单击此项可展开图像")](external-authentication-services/_static/image72.png)

等待与 Visual Studio 2017 创建你的项目。

<!-- [![](external-authentication-services/_static/image12.png "Click to Expand the Image")](external-authentication-services/_static/image11.png) -->

完成后创建你的项目的 Visual Studio 2017，打开*Startup.Auth.cs*文件，位于**应用\_启动**文件夹。

当你首次创建项目时，没有任何外部身份验证服务中启用*Startup.Auth.cs*文件; 下面将说明如何在代码可能类似于，有关在何处突出显示部分会启用此功能外部身份验证服务和任何相关设置才能在 ASP.NET 应用程序中使用 Microsoft 帐户、 Twitter、 Facebook 或 Google 身份验证：

[!code-csharp[Main](external-authentication-services/samples/sample1.cs)]

按 F5 以生成和调试 web 应用程序时，它将显示登录屏幕，你将看到尚未定义任何外部身份验证服务。

[![](external-authentication-services/_static/image73.png "单击此项可展开图像")](external-authentication-services/_static/image73.png)

在以下部分中，将了解如何启用每个使用 Visual Studio 2017 中的 ASP.NET 提供的外部身份验证服务。

<a id="FACEBOOK"></a>
## <a name="enabling-facebook-authentication"></a>启用 Facebook 身份验证

使用 Facebook 身份验证要求你创建 Facebook 开发人员帐户，并且你的项目将需要应用程序 ID 和机密密钥从 Facebook 的函数。 有关创建 Facebook 开发人员帐户和获取应用程序 ID 和机密密钥的信息，请参阅[ https://go.microsoft.com/fwlink/?LinkID=252166 ](https://go.microsoft.com/fwlink/?LinkID=252166)。

你一次获取你的应用程序 ID 和机密密钥，请使用以下步骤启用 web 应用程序的 Facebook 身份验证：

1. 在 Visual Studio 2017 中打开你的项目时，打开*Startup.Auth.cs*文件。

2. 查找代码的 Facebook 身份验证部分：

    [!code-csharp[Main](external-authentication-services/samples/sample2.cs)]
3. 删除&quot; // &quot;字符以取消注释的代码中，突出显示的行，然后添加你的应用程序 ID 和机密密钥。 添加这些参数后，可以重新编译你的项目：

    [!code-csharp[Main](external-authentication-services/samples/sample3.cs)]
4. 按 F5 以在 web 浏览器中打开 web 应用程序时，你将看到 Facebook 已被定义为外部身份验证服务：

    [![](external-authentication-services/_static/image74.png "单击此项可展开图像")](external-authentication-services/_static/image74.png)
5. 当您单击**Facebook**按钮，在浏览器将重定向到 Facebook 登录页：

    [![](external-authentication-services/_static/image22.png "单击此项可展开图像")](external-authentication-services/_static/image21.png)
6. 输入你的 Facebook 凭据并单击后**登录**，在 web 浏览器将重定向回到 web 应用程序，这会提示您输入**用户名**你想要将与相关联你Facebook 帐户：

    [![](external-authentication-services/_static/image24.png "单击此项可展开图像")](external-authentication-services/_static/image23.png)
7. 在输入您的用户名称并单击后**注册**按钮，web 应用程序将显示默认**主页**为你的 Facebook 帐户：

    [![](external-authentication-services/_static/image26.png "单击此项可展开图像")](external-authentication-services/_static/image25.png)

<a id="GOOGLE"></a>
## <a name="enabling-google-authentication"></a>启用 Google 身份验证

使用 Google 身份验证要求你创建一个 Google 开发人员帐户，并且你的项目将需要应用程序 ID 和机密密钥来自 Google 的函数。 有关创建 Google 开发人员帐户和获取应用程序 ID 和机密密钥的信息，请参阅[ https://developers.google.com ](https://developers.google.com)。


若要启用 web 应用程序的 Google 身份验证，请使用以下步骤：

1. 在 Visual Studio 2017 中打开你的项目时，打开*Startup.Auth.cs*文件。

2. 查找代码的 Google 身份验证部分：

    [!code-csharp[Main](external-authentication-services/samples/sample4.cs)]
3. 删除&quot; // &quot;字符以取消注释的代码中，突出显示的行，然后添加你的应用程序 ID 和机密密钥。 添加这些参数后，可以重新编译你的项目：

    [!code-csharp[Main](external-authentication-services/samples/sample5.cs)]
4. 按 F5 以在 web 浏览器中打开 web 应用程序时，你将看到 Google 已被定义为外部身份验证服务：

    [![](external-authentication-services/_static/image75.png "单击此项可展开图像")](external-authentication-services/_static/image75.png)
5. 当您单击**Google**按钮，在浏览器将重定向到 Google 登录页：

    [![](external-authentication-services/_static/image32.png "单击此项可展开图像")](external-authentication-services/_static/image31.png)
6. 输入你的 Google 凭据并单击后**登录**，Google 将提示你验证你的 web 应用程序有权访问你的 Google 帐户：

    [![](external-authentication-services/_static/image34.png "单击此项可展开图像")](external-authentication-services/_static/image33.png)
7. 当您单击**Accept**，在 web 浏览器将重定向回到 web 应用程序，这会提示您输入**用户名**你想要将与你的 Google 帐户相关联：

    [![](external-authentication-services/_static/image36.png "单击此项可展开图像")](external-authentication-services/_static/image35.png)
8. 在输入您的用户名称并单击后**注册**按钮，web 应用程序将显示默认**主页**为你的 Google 帐户：

    [![](external-authentication-services/_static/image38.png "单击此项可展开图像")](external-authentication-services/_static/image37.png)

<a id="MICROSOFT"></a>
## <a name="enabling-microsoft-authentication"></a>启用 Microsoft 身份验证

Microsoft 身份验证要求你创建开发人员帐户，并且它需要客户端 ID 和客户端机密才能正常。 有关创建 Microsoft 开发人员帐户和获取客户端 ID 和客户端机密的信息，请参阅[ https://go.microsoft.com/fwlink/?LinkID=144070 ](https://go.microsoft.com/fwlink/?LinkID=144070)。

你一次获取使用者密钥和使用者机密，请使用以下步骤启用 web 应用程序的 Microsoft 身份验证：

1. 在 Visual Studio 2017 中打开你的项目时，打开*Startup.Auth.cs*文件。

2. 查找代码的 Microsoft 身份验证部分：

    [!code-csharp[Main](external-authentication-services/samples/sample6.cs)]
3. 删除&quot; // &quot;字符以取消注释的代码中，突出显示的行，然后添加你的客户端 ID 和客户端机密。 添加这些参数后，可以重新编译你的项目：

    [!code-csharp[Main](external-authentication-services/samples/sample7.cs)]
4. 按 F5 以在 web 浏览器中打开 web 应用程序时，你将看到 Microsoft 已被定义为外部身份验证服务：

    [![](external-authentication-services/_static/image42.png "单击此项可展开图像")](external-authentication-services/_static/image41.png)
5. 当您单击**Microsoft**按钮，在浏览器将重定向到 Microsoft 登录页：

    [![](external-authentication-services/_static/image44.png "单击此项可展开图像")](external-authentication-services/_static/image43.png)
6. 输入 Microsoft 凭据并单击后**登录**，系统会提示以验证你的 web 应用程序有权访问你的 Microsoft 帐户：

    [![](external-authentication-services/_static/image46.png "单击此项可展开图像")](external-authentication-services/_static/image45.png)
7. 当您单击**是**，在 web 浏览器将重定向回到 web 应用程序，这会提示您输入**用户名**你想要将与你的 Microsoft 帐户相关联：

    [![](external-authentication-services/_static/image48.png "单击此项可展开图像")](external-authentication-services/_static/image47.png)
8. 在输入您的用户名称并单击后**注册**按钮，web 应用程序将显示默认**主页**你的 Microsoft 帐户：

    [![](external-authentication-services/_static/image50.png "单击此项可展开图像")](external-authentication-services/_static/image49.png)

<a id="TWITTER"></a>
## <a name="enabling-twitter-authentication"></a>启用 Twitter 身份验证

Twitter 身份验证要求你创建开发人员帐户，并且它需要使用者密钥和使用者机密才能正常。 有关创建 Twitter 开发人员帐户并获取使用者密钥和使用者机密的信息，请参阅[ https://go.microsoft.com/fwlink/?LinkID=252166 ](https://go.microsoft.com/fwlink/?LinkID=252166)。

你一次获取使用者密钥和使用者机密，请使用以下步骤启用 Twitter 身份验证 web 应用程序：

1. 在 Visual Studio 2017 中打开你的项目时，打开*Startup.Auth.cs*文件。

2. 找到 Twitter 身份验证代码的一部分：

    [!code-csharp[Main](external-authentication-services/samples/sample8.cs)]
3. 删除&quot; // &quot;字符，取消注释的代码中，突出显示的行，并添加使用者密钥和使用者机密。 添加这些参数后，可以重新编译你的项目：

    [!code-csharp[Main](external-authentication-services/samples/sample9.cs)]
4. 按 F5 以在 web 浏览器中打开 web 应用程序时，你将看到 Twitter 已被定义为外部身份验证服务：

    [![](external-authentication-services/_static/image54.png "单击此项可展开图像")](external-authentication-services/_static/image53.png)
5. 当您单击**Twitter**按钮，在浏览器将重定向到 Twitter 登录页：

    [![](external-authentication-services/_static/image56.png "单击此项可展开图像")](external-authentication-services/_static/image55.png)
6. 输入你的 Twitter 凭据，然后单击后**授权应用**，在 web 浏览器将重定向回到 web 应用程序，这会提示您输入**用户名**你想要将与相关联你的 Twitter 帐户：

    [![](external-authentication-services/_static/image58.png "单击此项可展开图像")](external-authentication-services/_static/image57.png)
7. 在输入您的用户名称并单击后**注册**按钮，web 应用程序将显示默认**主页**使用 Twitter 帐户：

    [![](external-authentication-services/_static/image60.png "单击此项可展开图像")](external-authentication-services/_static/image59.png)

<a id="MOREINFO"></a>
## <a name="additional-information"></a>其他信息

有关创建使用 OAuth 和 OpenID 的应用程序的其他信息，请参阅以下 Url:

- [https://go.microsoft.com/fwlink/?LinkID=252166](https://go.microsoft.com/fwlink/?LinkID=252166)
- [https://go.microsoft.com/fwlink/?LinkID=243995](https://go.microsoft.com/fwlink/?LinkID=243995)

<a id="COMBINE"></a>
### <a name="combining-external-authentication-services"></a>合并外部身份验证服务

为更大的灵活性，可以在同一时间定义多个外部身份验证服务-这允许你的 web 应用程序的用户使用来自任何启用的外部身份验证服务的帐户：

[![](external-authentication-services/_static/image62.png "单击此项可展开图像")](external-authentication-services/_static/image61.png)

<a id="FQDN"></a>
### <a name="configure-iis-express-to-use-a-fully-qualified-domain-name"></a>配置 IIS Express 以使用完全限定的域名

某些外部身份验证提供程序不支持使用类似的 HTTP 地址来测试你的应用程序`http://localhost:port/`。 若要解决此问题，可以添加到主机文件的完全限定域名 (FQDN) 的静态映射并在 Visual Studio 2017，若要使用 FQDN 作为测试/调试配置项目的选项。 为此，请使用以下步骤：

- 添加映射的主机文件的静态 FQDN:

  1. 在 Windows 中打开提升的命令提示符。
  2. 键入以下命令：

      <kbd>记事本 %WinDir%\system32\drivers\etc\hosts</kbd>
  3. 类似于以下条目添加到主机文件中：

      <kbd>127.0.0.1 www.wingtiptoys.com</kbd>
  4. 保存并关闭 HOSTS 文件。

- 将 Visual Studio 项目配置为使用 FQDN:

  1. 在 Visual Studio 2017 中打开你的项目时，单击**项目**菜单，然后选择你的项目的属性。 例如，可以选择**WebApplication1 属性**。
  2. 选择**Web**选项卡。
  3. 输入的 FQDN<strong>项目 Url</strong>。 例如，应输入<kbd> <http://www.wingtiptoys.com> </kbd> ，如果已添加到主机文件的 FQDN 映射。

- 配置 IIS Express 以使用 FQDN 作为你的应用程序：

    1. 在 Windows 中打开提升的命令提示符。
    2. 键入以下命令以将更改为在 IIS Express 的文件夹：

        <kbd>cd /d &quot;%ProgramFiles%\IIS Express&quot;</kbd>
    3. 键入以下命令以将 FQDN 添加到你的应用程序：

        <kbd>appcmd.exe set config -section:system.applicationHost/sites /+&quot;[name='WebApplication1'].bindings.[protocol='http',bindingInformation='*:80:www.wingtiptoys.com']&quot; /commit:apphost</kbd>

  其中**WebApplication1**是你的项目的名称和**bindingInformation**包含你想要用于测试的端口号和 FQDN。

<a id="OBTAIN"></a>
### <a name="how-to-obtain-your-application-settings-for-microsoft-authentication"></a>如何获取 Microsoft 身份验证应用程序设置

链接到 Windows Live 进行 Microsoft 身份验证的应用程序是一个简单的过程。 如果已链接到 Windows Live 应用程序，可以使用以下步骤：

1. 浏览到[ https://go.microsoft.com/fwlink/?LinkID=144070 ](https://go.microsoft.com/fwlink/?LinkID=144070)并输入你的 Microsoft 帐户名和密码出现提示时，然后单击**登录**:

   <!--  [![](external-authentication-services/_static/image64.png "Click to Expand the Image")](external-authentication-services/_static/image63.png) -->
2. 选择**将应用添加**并输入出现提示时，应用程序的名称，然后单击**创建**:

    [![](external-authentication-services/_static/image79.png "单击此项可展开图像")](external-authentication-services/_static/image79.png)
3. 选择你的应用下**名称**并显示其应用程序属性页面。

4. 输入你的应用程序的重定向域。 复制**应用程序 ID**并在**应用程序机密**，选择**生成密码**。 复制显示的密码。 应用程序 ID 和密码是你的客户端 ID 和客户端机密。 选择**确定**，然后**保存**。

    [![](external-authentication-services/_static/image77.png "单击此项可展开图像")](external-authentication-services/_static/image77.png)

<a id="DISABLE"></a>
### <a name="optional-disable-local-registration"></a>可选：禁用本地注册

当前 ASP.NET 本地注册功能不会阻止自动的程序 （机器人） 帐户; 创建成员例如，通过使用智能机器人应用程序防护和验证等技术[CAPTCHA](../../../web-pages/overview/security/16-adding-security-and-membership.md)。 因此，应删除的登录页上的本地登录窗体和注册链接。 若要执行此操作，打开 *\_Login.cshtml*在项目中，页上，然后注释掉本地登录面板和注册链接的行。 在生成的页面应如下面的代码示例所示：

[!code-html[Main](external-authentication-services/samples/sample10.html)]

一旦已禁用本地登录面板和注册链接，您的登录页将仅显示已启用的外部身份验证提供程序：

[![](external-authentication-services/_static/image70.png "单击此项可展开图像")](external-authentication-services/_static/image69.png)
