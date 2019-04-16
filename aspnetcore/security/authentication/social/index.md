---
title: ASP.NET Core 中的 Facebook、Google 和外部提供程序身份验证
author: rick-anderson
description: 本教程演示如何使用外部身份验证提供程序通过 OAuth 2.0 生成 ASP.NET Core 2.x 应用。
ms.author: riande
ms.custom: mvc
ms.date: 4/19/2019
uid: security/authentication/social/index
ms.openlocfilehash: 61482481358256dc9ddd1a0a894541040a8a452f
ms.sourcegitcommit: 9b7fcb4ce00a3a32e153a080ebfaae4ef417aafa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59516321"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>ASP.NET Core 中的 Facebook、Google 和外部提供程序身份验证

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

本教程演示如何生成 ASP.NET Core 2.2 应用，该应用可让用户使用外部身份验证提供程序提供的凭据通过 OAuth 2.0 登录。

以下几节中介绍了 [Facebook](xref:security/authentication/facebook-logins)、[Twitter](xref:security/authentication/twitter-logins)、[Google](xref:security/authentication/google-logins) 和 [Microsoft](xref:security/authentication/microsoft-logins) 提供程序。 第三方程序包中提供了其他提供程序，例如 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 和 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)。

![Facebook、Twitter、Google plus 和 Windows 的社交媒体图标](index/_static/social.png)

让用户使用其现有凭据登录的好处：
* 方便用户操作。
* 将管理登录流程的许多复杂性转移到了第三方。 

有关社交登录如何驱动流量和客户转换的示例，请参阅 [Facebook](https://www.facebook.com/unsupportedbrowser) 和 [Twitter](https://dev.twitter.com/resources/case-studies) 的案例分析。

## <a name="create-a-new-aspnet-core-project"></a>创建新的 ASP.NET Core 项目

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”。
* 创建新的 ASP.NET Core Web 应用呈现。
* 在下拉列表中选择“ASP.NET Core 2.2”，然后选择“Web 应用程序”。
* 选择“更改身份验证”并设置针对“单个用户帐户”的身份验证。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。

* 将目录更改为 (`cd`) 包含项目的文件夹。

* 运行以下命令：

  ```console
  dotnet new webapp -o WebApp1
  code -r WebApp1
  ```

  * `dotnet new` 命令可在“WebApp1”文件夹中创建新的 Razor Pages 项目。
  * `code` 命令将在新 Visual Studio Code 实例中打开“WebApp1”文件夹。

  一个对话框随即出现，其中包含：“‘WebApp1’中缺少进行生成和调试所需的资产。是否添加它们?”

* 选择“是”

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

从终端运行以下命令：

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o WebApp1
```

上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。

## <a name="open-the-project"></a>打开项目

在 Visual Studio 中，选择“文件”>“打开”，然后选择“WebApp1.csproj”文件。

<!-- End of VS tabs -->

---

## <a name="apply-migrations"></a>应用迁移

* 运行应用并选择“注册”链接。
* 输入新帐户的电子邮件地址和密码，再选择“注册”。
* 按照说明操作来应用迁移。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>使用 SecretManager 存储登录提供程序分配的令牌

社交登录提供程序在注册过程中分配“应用程序 ID”和“应用程序机密”。 确切的令牌名称因提供程序而异。 这些令牌代表应用用来访问其 API 的凭据。 令牌构成“机密”，可利用[机密管理器](xref:security/app-secrets#secret-manager)将其链接到应用配置。 机密管理器是在配置文件（例如 appsettings.json）中存储令牌更安全替代方法。

> [!IMPORTANT]
> 机密管理器仅用于开发目的。 可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。

按照[在 ASP.NET Core 中进行开发期间安全存储应用机密](xref:security/app-secrets)主题中的步骤进行操作，以便存储以下每个登录提供程序分配的令牌。

## <a name="setup-login-providers-required-by-your-application"></a>应用程序所需的安装登录提供程序

使用以下主题配置应用程序，以使用相应的提供程序：

* [Facebook](xref:security/authentication/facebook-logins) 相关说明
* [Twitter](xref:security/authentication/twitter-logins) 相关说明
* [Google](xref:security/authentication/google-logins) 相关说明
* [Microsoft](xref:security/authentication/microsoft-logins) 相关说明
* [其他提供程序](xref:security/authentication/otherlogins)相关说明

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>选择性地设置密码

使用外部登录提供程序注册，即表明还没有向应用注册密码。 这可让用户无需创建和记住站点密码，但也会使用户依赖外部登录提供程序。 如果外部登录提供程序不可用，则无法登录网站。

使用外部提供程序在登录过程中设置的电子邮箱创建密码和登录：

* 选择右上角的“Hello &lt;电子邮件别名&gt;”链接，导航到“管理”视图。

![Web 应用程序“管理”视图](index/_static/pass1a.png)

* 选择“创建”

![“设置密码”页](index/_static/pass2a.png)

* 设置一个有效密码，可以用此密码和邮箱登录。

## <a name="next-steps"></a>后续步骤

* 本文介绍了外部身份验证，并说明了向 ASP.NET Core 应用添加外部登录所需的先决条件。

* 引用特定于提供程序的页，为应用所需的提供程序配置登录。

* 可能需要保留有关用户及其访问和刷新令牌的其他数据。 有关更多信息，请参见<xref:security/authentication/social/additional-claims>。
