---
title: 中的 web Api 使用 Azure Active Directory B2C 在 ASP.NET Core 中的身份验证
author: camsoper
description: 了解如何设置与 ASP.NET Core Web API 的 Azure Active Directory B2C 身份验证。 经过身份验证的 web API 使用 Postman 进行测试。
ms.author: casoper
ms.date: 09/21/2018
ms.custom: mvc, seodec18
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: c917a5130857165716bd801ac19dad0f53a7d214
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815380"
---
# <a name="authentication-in-web-apis-with-azure-active-directory-b2c-in-aspnet-core"></a>中的 web Api 使用 Azure Active Directory B2C 在 ASP.NET Core 中的身份验证

作者：[Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) 是云标识管理解决方案，适用于 web 和移动应用。 该服务提供用于在云中和本地托管的应用的身份验证。 身份验证类型包括个人帐户，社交网络帐户和联合企业帐户。 Azure AD B2C 还提供了最小配置多重身份验证。

Azure Active Directory (Azure AD) 和 Azure AD B2C 是单独的产品产品/服务。 Azure AD 租户表示组织，而 Azure AD B2C 租户表示与信赖方应用程序将使用的集合。 若要了解详细信息，请参阅[Azure AD B2C:常见问题 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)。

由于 web Api 有没有用户界面，因此它们无法将用户重定向到安全令牌服务等 Azure AD B2C。 相反，API 是具有已验证了用户与 Azure AD B2C 将调用应用程序中传递的持有者令牌。 API 会验证该令牌而无需直接用户交互。

在本教程中，学习如何：

> [!div class="checklist"]
> * 创建 Azure Active Directory B2C 租户。
> * 在 Azure AD B2C 中注册 Web API。
> * 使用 Visual Studio 来创建 Web API 配置为使用 Azure AD B2C 租户进行身份验证。
> * 配置控制的 Azure AD B2C 租户行为的策略。
> * 使用 Postman 来模拟一个 web 应用，用于显示登录对话框中，检索令牌，并使用它来对 web API 发出请求。

## <a name="prerequisites"></a>系统必备

本演练需要以下项：

* [Microsoft Azure 订阅](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>创建 Azure Active Directory B2C 租户

创建一个 Azure AD B2C 租户[文档中所述](/azure/active-directory-b2c/active-directory-b2c-get-started)。 出现提示时，将租户与 Azure 订阅相关联是可选的用于本教程。

## <a name="configure-a-sign-up-or-sign-in-policy"></a>配置注册或登录策略

使用到的 Azure AD B2C 文档中的步骤[创建注册或登录策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)。 将策略命名**SiUpIn**。  使用提供的文档中的示例值**标识提供者**，**注册属性**，并**应用程序声明**。 使用**立即运行**是可选的按钮以测试策略，如文档中所述。

## <a name="register-the-api-in-azure-ad-b2c"></a>在 Azure AD B2C 中注册 API

在新创建的 Azure AD B2C 租户中注册 API 通过[文档中的步骤](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)下**注册 web API**部分。

使用以下值：

| 设置                       | 值               | 说明                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **名称**                      | *{API 名称}*        | 输入**名称**描述你的应用向使用者的应用。                     |
| **包括 web 应用 /web API** | 是                 |                                                                                        |
| **允许隐式流**       | 是                 |                                                                                        |
| **回复 URL**                 | `https://localhost` | 回复 Url 属于终结点，Azure AD B2C 在其中返回应用请求的任何令牌。 |
| **应用程序 ID URI**                | *api*               | 该 URI 不需要解析为物理地址。 它只需是唯一的。     |
| **包含本机客户端**     | 否                  |                                                                                        |

注册 API 后，将显示在租户中的应用和 Api 的列表。 选择以前注册的 API。 选择**副本**右侧的图标**应用程序 ID**字段以将其复制到剪贴板。 选择**发布的作用域**，并验证默认*user_impersonation*作用域是存在。

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>在 Visual Studio 中创建 ASP.NET Core 应用

Visual Studio Web 应用程序模板可以配置为使用 Azure AD B2C 租户进行身份验证。

在 Visual Studio 中：

1. 创建新的 ASP.NET Core Web 应用程序。 
2. 选择**Web API**从模板列表。
3. 选择**更改身份验证**按钮。

    ![更改身份验证按钮](./azure-ad-b2c-webapi/change-auth-button.png)

4. 在中**更改身份验证**对话框中，选择**单个用户帐户** > **连接到现有用户存储在云中**。

    ![更改身份验证对话框](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. 完成窗体具有以下值：

    | 设置                       | 值                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **域名**               | *{域在 B2C 租户名称}*                |
    | **应用程序 ID**            | *{粘贴剪贴板中的应用程序 ID}*       |
    | **注册或登录策略** | B2C_1_SiUpIn                                          |

    选择**确定**以关闭**更改身份验证**对话框。 选择**确定**创建 web 应用。

Visual Studio 创建 web API 的具有控制器名为*ValuesController.cs*返回硬编码值的 GET 请求。 类用修饰[Authorize 属性](xref:security/authorization/simple)，因此所有请求都需要身份验证。

## <a name="run-the-web-api"></a>运行 web API

在 Visual Studio 中，运行 API。 Visual Studio 启动浏览器指向 API 的根 URL。 请注意地址栏中的 URL 并将在后台运行的 API。

> [!NOTE]
> 由于没有为根 URL 定义没有控制器，浏览器可能显示 404 （找不到页） 错误。 这是预期行为。

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>使用 Postman 来获取令牌，并测试 API

[Postman](https://getpostman.com/postman)是工具用于测试 web Api。 对于本教程，Postman 模拟访问 web API 代表该用户的 web 应用程序。

### <a name="register-postman-as-a-web-app"></a>注册为 web 应用的 Postman

Postman 模拟 web 应用从 Azure AD B2C 租户中获取令牌，因为它必须注册在租户中为 web 应用。 注册使用 Postman[文档中的步骤](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)下**注册 web 应用**部分。 在停止**创建 web 应用客户端机密**部分。 本教程不需要客户端机密。 

使用以下值：

| 设置                       | 值                            | 说明                           |
|-------------------------------|----------------------------------|---------------------------------|
| **名称**                      | Postman                          |                                 |
| **包括 web 应用 /web API** | 是                              |                                 |
| **允许隐式流**       | 是                              |                                 |
| **回复 URL**                 | `https://getpostman.com/postman` |                                 |
| **应用程序 ID URI**                | *{留}*                  | 对于本教程不被必需。 |
| **包含本机客户端**     | 否                               |                                 |

新注册的 web 应用需要访问 web API 代表该用户的权限。  

1. 选择**Postman**的应用程序，然后选择列表中**API 访问**在左侧菜单中。
1. 选择 **+ 添加**。
1. 在中**选择 API**下拉列表中，选择 web API 的名称。
1. 在中**选择作用域**下拉列表中，确保选中所有作用域。
1. 选择**确定**。

请注意 Postman 应用的应用程序 ID，因为它所需获取持有者令牌。

### <a name="create-a-postman-request"></a>创建 Postman 请求

启动 Postman。 默认情况下，显示 Postman**创建新**对话框后启动。 如果未显示对话框中，选择 **+ 新建**在左上角的按钮。

从**创建新**对话框：

1. 选择**请求**。

    ![请求按钮](./azure-ad-b2c-webapi/postman-create-new.png)

2. 输入*获取值*中**请求名称**框。
3. 选择 **+ 创建集合**若要创建用于存储请求新的集合。 命名集合*ASP.NET Core 教程*，然后选择复选标记。

    ![创建新的集合](./azure-ad-b2c-webapi/postman-create-collection.png)

4. 选择**将保存到 ASP.NET Core 教程**按钮。

### <a name="test-the-web-api-without-authentication"></a>测试 web API 而无需身份验证

若要验证 web API 需要身份验证，首先请不带身份验证的请求。

1. 在中**输入请求 URL**框中，输入的 URL `ValuesController`。 URL 是使用浏览器中显示的相同**api/values**追加。 例如 `https://localhost:44375/api/values`。
2. 选择**发送**按钮。
3. 请注意响应的状态是*401 未授权*。

    ![401 未授权的响应](./azure-ad-b2c-webapi/postman-401-status.png)

> [!IMPORTANT]
> 如果收到"无法获取任何响应"的错误，可能需要禁用中的 SSL 证书验证[Postman 设置](https://learning.getpostman.com/docs/postman/launching_postman/settings)。

### <a name="obtain-a-bearer-token"></a>获取持有者令牌

若要向 web API 发出的经过身份验证的请求，持有者令牌是必需的。 Postman 可轻松地登录到 Azure AD B2C 租户并获取的令牌。

1. 上**授权**选项卡上，在**类型**下拉列表中，选择**OAuth 2.0**。 在中**授权将数据添加到**下拉列表中，选择**请求标头**。 选择**获取新访问令牌**。

    ![使用设置的授权选项卡](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. 完成**获取新访问令牌**，如下所示的对话框：

   |                设置                 |                                             值                                             |                                                                                                                                    说明                                                                                                                                     |
   |----------------------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |      **令牌名称**       |                                          *{令牌名称}*                                       |                                                                                                                   输入令牌的描述性名称。                                                                                                                    |
   |      **授权类型**       |                                           隐式                                            |                                                                                                                                                                                                                                                                              |
   |     **回调 URL**      |                                 `https://getpostman.com/postman`                              |                                                                                                                                                                                                                                                                              |
   |       **身份验证 URL**        | `https://login.microsoftonline.com/{tenant domain name}/oauth2/v2.0/authorize?p=B2C_1_SiUpIn` |  替换 *{租户名称}* 与租户的域名。 **重要**:此 URL 必须具有相同域名中找到的内容作为`AzureAdB2C.Instance`中 web API 的*appsettings.json*文件。 请参阅备注&dagger;。                                                  |
   |       **客户端 ID**       |                *{输入 Postman 应用**应用程序 ID**}*                              |                                                                                                                                                                                                                                                                              |
   |         **范围**         |         `https://{tenant domain name}/{api}/user_impersonation openid offline_access`       | 替换 *{租户名称}* 与租户的域名。 替换 *{api}* 替换应用程序 ID URI 在 web API 时提供首次注册 (在这种情况下， `api`)。 Url 模式是： `https://{tenant}.onmicrosoft.com/{api-id-uri}/{scope name}`。         |
   |         **状态**         |                                      *{留}*                                          |                                                                                                                                                                                                                                                                              |
   | **客户端身份验证** |                                在正文中发送客户端凭据                                |                                                                                                                                                                                                                                                                              |

    > [!NOTE]
    > &dagger; 在 Azure Active Directory B2C 门户中的策略设置对话框将显示两个可能的 Url:一个采用格式`https://login.microsoftonline.com/`{租户名称} / {附加路径信息} 和其他格式`https://{tenant name}.b2clogin.com/`{租户名称} / {附加路径信息}。 它具有**关键**域中在中找到`AzureAdB2C.Instance`中 web API 的*appsettings.json*文件与在 web 应用中使用*appsettings.json*文件。 这是用于表示在 Postman 中的身份验证 URL 字段的同一个域。 请注意，Visual Studio 将使用比在门户中显示的内容稍有不同的 URL 格式。 只要域匹配，适用于 URL。

3. 选择**请求令牌**按钮。

4. Postman 会打开一个包含 Azure AD B2C 租户的登录对话框的新窗口。 （如果已经创建了一个测试策略） 的现有帐户登录，或选择**立即注册**若要创建新帐户。 **忘记了密码？** 链接用于重置忘记的密码。

5. 已成功登录后，在窗口关闭并**管理访问令牌**此时将显示对话框。 向下的滚动到底部，选择**使用令牌**按钮。

    ![在哪里可以找到"使用令牌"按钮](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>测试 web API 身份验证

选择**发送**按钮再次发送该请求。 这一次，响应状态是*200 确定*，并在响应上可见的 JSON 有效负载**正文**选项卡。

![有效负载和成功状态](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Azure Active Directory B2C 租户。
> * 在 Azure AD B2C 中注册 Web API。
> * 使用 Visual Studio 来创建 Web API 配置为使用 Azure AD B2C 租户进行身份验证。
> * 配置控制的 Azure AD B2C 租户行为的策略。
> * 使用 Postman 来模拟一个 web 应用，用于显示登录对话框中，检索令牌，并使用它来对 web API 发出请求。

继续通过学习开发你的 API:

* [保护 web 应用使用 Azure AD B2C 的 ASP.NET Core](xref:security/authentication/azure-ad-b2c)。
* [从.NET web 应用中使用 Azure AD B2C 调用.NET web API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)。
* [自定义 Azure AD B2C 用户界面](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)。
* [配置密码复杂性要求](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)。
* [启用多重身份验证](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)。
* 配置其他标识提供者，例如[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)， [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)， [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)， [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)， [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)，等等。
* [使用 Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet)从 Azure AD B2C 租户中检索其他用户信息，例如组成员身份。
