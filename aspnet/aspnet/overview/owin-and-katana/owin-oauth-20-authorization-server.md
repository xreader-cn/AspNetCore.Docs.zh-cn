---
uid: aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
title: OWIN OAuth 2.0 授权服务器 |Microsoft Docs
author: hongyes
description: 本教程将指导您如何实现 OAuth 2.0 授权服务器使用 OWIN OAuth 中间件。 这是一个高级的教程，该唯一 outlin...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 20acee16-c70c-41e9-b38f-92bfcf9a4c1c
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
msc.type: authoredcontent
ms.openlocfilehash: b8451d2d9e346bd5e2f51ba45e48030a5221b549
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667643"
---
# <a name="owin-oauth-20-authorization-server"></a>OWIN OAuth 2.0 授权服务器

> 本教程将指导您如何实现 OAuth 2.0 授权服务器使用 OWIN OAuth 中间件。 这是一种高级的教程，仅简要介绍创建 OWIN OAuth 2.0 授权服务器的步骤。 这不是分步教程。 [下载示例代码](https://code.msdn.microsoft.com/OWIN-OAuth-20-Authorization-ba2b8783/file/114932/1/AuthorizationServer.zip)。
>
> > [!NOTE]
> > 此边框不符合预期要用于创建安全的生产应用。 本教程旨在提供仅概述了如何实现 OAuth 2.0 授权服务器使用 OWIN OAuth 中间件。
>
>
> ## <a name="software-versions"></a>软件版本
>
> | **本教程中所示** | **也可用于** |
> | --- | --- |
> | Windows 8.1 | Windows 10，Windows 8，Windows 7 |
> | [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> | .NET 4.7.2 |  |
>
> ## <a name="questions-and-comments"></a>问题和提出的意见
>
> 如果你有与本教程不直接相关的问题，可以将其在发布[Katana 项目 GitHub 上](https://github.com/aspnet/AspNetKatana/)。 有关的问题和意见关于教程本身，请参阅在页面底部的注释部分。


[OAuth 2.0 framework](http://tools.ietf.org/html/rfc6749)允许第三方应用程序以获取对 HTTP 服务的有限访问权限。 而不是使用资源所有者的凭据来访问受保护的资源，客户端获取访问令牌 (这是一个字符串，表示特定的作用域、 生存期和其他访问属性)。 访问令牌被颁发给第三方客户端通过授权服务器资源所有者的批准。

本教程介绍：

- 如何创建授权服务器以支持四种授权授予类型，以及刷新令牌：
    - 授权代码授予
    - 隐式授权
    - 资源所有者密码凭据授予
    - 客户端凭据授予
- 创建访问令牌通过受保护的资源服务器。
- 正在创建 OAuth 2.0 客户端。

<a id="prerequisites"></a>
## <a name="prerequisites"></a>系统必备

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)中所示**软件版本**页的顶部。
- 带 OWIN 的熟悉程度。 请参阅[Katana 项目入门](https://msdn.microsoft.com/magazine/dn451439.aspx)并[新增 OWIN 和 Katana](index.md)。
- 熟悉[OAuth](http://tools.ietf.org/html/rfc6749)术语中，其中包括[角色](http://tools.ietf.org/html/rfc6749#section-1.1)，[协议流](http://tools.ietf.org/html/rfc6749#section-1.2)，以及[授权授予](http://tools.ietf.org/html/rfc6749#section-1.3)。 [OAuth 2.0 简介](http://tools.ietf.org/html/rfc6749#section-1)提供了详细介绍。

## <a name="create-an-authorization-server"></a>创建授权服务器

在本教程中，我们将大致草图了解如何使用[OWIN](https://msdn.microsoft.com/magazine/dn451439.aspx)和 ASP.NET MVC 创建服务器的授权。 我们希望很快提供有关已完成的示例中，可供下载本教程不包括每个步骤。 首先，创建名为空的 web 应用*AuthorizationServer*并安装以下包：

- Microsoft.AspNet.Mvc
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth
- Microsoft.Owin.Security.Cookies
- Microsoft.Owin.Security.Google （或任何其他社交登录名打包 Microsoft.Owin.Security.Facebook 等）

添加[OWIN 启动类](owin-startup-class-detection.md)名为在项目根文件夹下*启动*。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample1.cs?highlight=8)]

创建*应用程序\_启动*文件夹。 选择*应用程序\_启动*文件夹，然后使用 Shift + Alt + A 若要添加的下载的版本*AuthorizationServer\App\_Start\Startup.Auth.cs*文件。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample2.cs)]

上面的代码使应用程序/外部登录 cookie 和 Google 身份验证，授权服务器本身用于管理帐户。

`UseOAuthAuthorizationServer`扩展方法是设置授权服务器。 安装程序选项包括：

- `AuthorizeEndpointPath`：请求路径，其中客户端应用程序会将重定向用户代理以获取用户同意使用颁发的令牌或代码。 它必须以具有前导斜杠，例如，"`/Authorize`"。
- `TokenEndpointPath`：请求路径客户端应用程序直接进行通信以获取访问令牌。 它必须以具有前导斜杠，如"/token"。 如果客户端颁发[客户端\_机密](http://tools.ietf.org/html/rfc6749#appendix-A.2)，它必须提供给此终结点。
- `ApplicationCanDisplayErrors`：设置为`true`如果 web 应用程序想要在生成的客户端验证错误的自定义错误页`/Authorize`终结点。 这才必需的情况下，在浏览器不会重定向回客户端应用程序，例如，当`client_id`或`redirect_uri`不正确。 `/Authorize`终结点应该会看到"oauth。错误"、"oauth。ErrorDescription"和"oauth。ErrorUri"属性添加到 OWIN 环境。

    > [!NOTE]
    > 如果不为 true，则授权服务器将返回默认错误页的错误详细信息。
- `AllowInsecureHttp`：为 true，则允许授权和令牌请求到达 HTTP URI 地址，并允许传入`redirect_uri`授权请求参数，具有 HTTP URI 地址。

    > [!WARNING]
    > 安全性-这是只能在开发。
- `Provider`：通过处理由授权服务器中间件引发事件的应用程序提供的对象。 应用程序可能完全实现接口，或它可能会造成的实例`OAuthAuthorizationServerProvider`并分配此服务器支持的 OAuth 流所需的委托。
- `AuthorizationCodeProvider`：生成一次性授权代码返回到客户端应用程序。 为 OAuth 服务器要保护的应用程序**必须**提供的一个实例`AuthorizationCodeProvider`由生成的令牌`OnCreate/OnCreateAsync`事件被视为有效只有一个调用`OnReceive/OnReceiveAsync`。
- `RefreshTokenProvider`：生成可用于生成新的访问令牌时所需的刷新令牌。 如果未提供授权服务器不会返回的刷新令牌`/Token`终结点。

## <a name="account-management"></a>帐户管理

OAuth 并不关心其中或如何管理用户帐户信息。 它具有[ASP.NET 标识](../../../identity/index.md)对其负责。 在本教程中，我们将简化帐户管理代码，并只需确保该用户可以登录使用 OWIN cookie 中间件。 下面是简化的示例代码`AccountController`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample3.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample4.cs?highlight=1)]

`ValidateClientRedirectUri` 用来验证其注册的重定向 URL 的客户端。 `ValidateClientAuthentication` 检查的基本方案标头和窗体正文以获取客户端的凭据。

登录页如下所示：

![](owin-oauth-20-authorization-server/_static/image1.png)

查看 IETF 的 OAuth 2[授权代码授予](http://tools.ietf.org/html/rfc6749#section-4.1)现在部分。

**提供程序**（在下表中） 是[OAuthAuthorizationServerOptions](https://msdn.microsoft.com/library/microsoft.owin.security.oauth.oauthauthorizationserveroptions(v=vs.111).aspx)。提供程序，其类型`OAuthAuthorizationServerProvider`，其中包含所有 OAuth 服务器事件。

| 从授权代码授予部分流步骤 | 下载示例将执行这些步骤： |
| --- | --- |
|  |  |
| （A） 客户端将定向到授权终结点资源所有者的用户代理，从而启动流。 客户端包括客户端标识符、 请求的作用域、 本地状态和重定向 URI 的授权服务器将用户代理返回以后，即可发送授予 （或拒绝） 访问。 | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| （B） 授权服务器进行身份验证资源所有者 （通过用户代理），并确定资源所有者是允许还是拒绝客户端的访问请求。 | **&lt;If user grants access&gt;** Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| （C） 假定资源所有者授予访问权限，授权服务器将用户代理重定向回客户端使用的重定向 URI 提供早期 （在请求中或客户端注册过程）。 ... |  |
|  |  |
| （D) 客户端通过包含上一步中收到的授权代码授权服务器的令牌终结点请求访问令牌。 当发出请求，客户端进行身份验证与授权服务器。 客户端会包括重定向 URI 用于获取验证的授权代码。 | Provider.MatchEndpoint Provider.ValidateClientAuthentication AuthorizationCodeProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantAuthorizationCode Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |

有关实现示例`AuthorizationCodeProvider.CreateAsync`和`ReceiveAsync`以控制创建和验证授权代码如下所示。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample5.cs)]

上面的代码使用内存中并发字典来存储代码和标识票证和收到代码后还原标识。 在实际的应用程序，它将由永久性数据存储替换。 授权终结点适用于资源所有者授予对客户端访问权限。 通常情况下，它需要一个用户界面来允许用户单击一个按钮，然后确认授权。 OWIN OAuth 中间件允许应用程序代码来处理授权终结点。 在我们的示例应用，我们使用名为一个 MVC 控制器`OAuthController`若要对其进行处理。 以下是示例实现：

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample6.cs?highlight=15)]

`Authorize`操作将首先检查是否用户已登录到授权服务器。 如果没有，身份验证中间件质询调用方进行身份验证使用"应用程序"cookie 并将重定向到登录页。 （请参阅上面的突出显示的代码。）如果用户已登录，它会呈现授权视图中，如下所示：

![](owin-oauth-20-authorization-server/_static/image2.png)

如果**Grant**按钮处于选中状态，`Authorize`操作将创建新的"Bearer"标识和使用它登录。 它将触发授权服务器生成的持有者令牌并将其发送回客户端与 JSON 有效负载。

### <a name="implicit-grant"></a>隐式授权

请参阅 IETF 的 OAuth 2[隐式授权](http://tools.ietf.org/html/rfc6749#section-4.2)现在部分。

 [隐式授权](http://tools.ietf.org/html/rfc6749#section-4.2)图 4 所示的流是流程和映射的 OWIN OAuth 中间件遵循。

| 流步骤，可从隐式授权部分 | 下载示例将执行这些步骤： |
| --- | --- |
|  |  |
| （A） 客户端将定向到授权终结点资源所有者的用户代理，从而启动流。 客户端包括客户端标识符、 请求的作用域、 本地状态和重定向 URI 的授权服务器将用户代理返回以后，即可发送授予 （或拒绝） 访问。 | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| （B） 授权服务器进行身份验证资源所有者 （通过用户代理），并确定资源所有者是允许还是拒绝客户端的访问请求。 | **&lt;If user grants access&gt;** Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| （C） 假定资源所有者授予访问权限，授权服务器将用户代理重定向回客户端使用的重定向 URI 提供早期 （在请求中或客户端注册过程）。 ... |  |
|  |  |
| （D) 客户端通过包含上一步中收到的授权代码授权服务器的令牌终结点请求访问令牌。 当发出请求，客户端进行身份验证与授权服务器。 客户端会包括重定向 URI 用于获取验证的授权代码。 |  |

由于我们已实现授权终结点 (`OAuthController.Authorize`操作) 的授权代码授予，它会自动启用隐式流。 注意：`Provider.ValidateClientRedirectUri`用来验证其重定向 URL，用于防止隐式授权流发送访问令牌来恶意客户端的客户端 ID ([拦截的攻击](https://www.owasp.org/index.php/Man-in-the-middle_attack))。

### <a name="resource-owner-password-credentials-grant"></a>资源所有者密码凭据授予

请参阅 IETF 的 OAuth 2[资源所有者密码凭据授予](http://tools.ietf.org/html/rfc6749#section-4.3)现在部分。

 [资源所有者密码凭据授予](http://tools.ietf.org/html/rfc6749#section-4.3)图 5 所示的流是流程和映射的 OWIN OAuth 中间件遵循。

| 从资源所有者密码凭据授予部分流步骤 | 下载示例将执行这些步骤： |
| --- | --- |
|  |  |
| （A） 资源所有者向客户端提供其用户名和密码。 |  |
|  |  |
| （B） 客户端通过包括从资源所有者处收到的凭据从授权服务器的令牌终结点请求访问令牌。 当发出请求，客户端进行身份验证与授权服务器。 | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantResourceOwnerCredentials Provider.TokenEndpoint AccessToken Provider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| （C） 授权服务器对客户端进行身份验证并验证资源所有者的凭据和有效的情况下颁发访问令牌。 |  |

下面是示例实现`Provider.GrantResourceOwnerCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample7.cs)]

> [!NOTE]
> 上面的代码将解释在本教程的本部分，因此不应在安全或生产应用。 它不会检查资源所有者凭据。 它假定每个凭据有效，并为其创建一个新的标识。 新的标识将用于生成访问令牌和刷新令牌。 请将代码替换你自己的安全帐户管理代码。


### <a name="client-credentials-grant"></a>客户端凭据授予

请参阅 IETF 的 OAuth 2[客户端凭据授予](http://tools.ietf.org/html/rfc6749#section-4.4)现在部分。

 [客户端凭据授予](http://tools.ietf.org/html/rfc6749#section-4.4)图 6 所示的流是流程和映射的 OWIN OAuth 中间件遵循。

| 从客户端凭据授予部分流步骤 | 下载示例将执行这些步骤： |
| --- | --- |
|  |  |
| （A) 客户端向授权服务器进行身份验证，并从令牌终结点请求访问令牌。 | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantClientCredentials Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| （B） 授权服务器进行身份验证客户端，并有效的情况下颁发访问令牌。 |  |

下面是示例实现`Provider.GrantClientCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample8.cs)]

> [!NOTE]
> 上面的代码将解释在本教程的本部分，因此不应在安全或生产应用。 请将代码替换你自己的安全客户端管理代码。


### <a name="refresh-token"></a>刷新令牌

请参阅 IETF 的 OAuth 2[刷新令牌](http://tools.ietf.org/html/rfc6749#section-1.5)现在部分。

 [刷新令牌](http://tools.ietf.org/html/rfc6749#section-1.5)图 2 所示的流是流程和映射的 OWIN OAuth 中间件遵循。

| 从客户端凭据授予部分流步骤 | 下载示例将执行这些步骤： |
| --- | --- |
|  |  |
| （G) 客户端通过使用授权服务器进行身份验证和提供刷新令牌请求新的访问令牌。 客户端身份验证要求基于客户端类型和授权服务器策略。 | Provider.MatchEndpoint Provider.ValidateClientAuthentication RefreshTokenProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantRefreshToken Provider.TokenEndpoint AccessTokenProvider.CreateAsyncRefreshTokenProvider.CreateAsync |
|  |  |
| （H） 授权服务器对客户端进行身份验证并验证刷新令牌和有效的情况下颁发新的访问令牌 （和 （可选） 新的刷新令牌）。 |  |

下面是示例实现`Provider.GrantRefreshToken`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample9.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample10.cs)]

## <a name="create-a-resource-server-which-is-protected-by-access-token"></a>创建访问令牌通过受保护的资源服务器

创建空 web 应用程序项目并安装以下项目中的包：

- Microsoft.AspNet.WebApi.Owin
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth

创建一个 startup 类，并配置身份验证和 Web API。 请参阅*AuthorizationServer\ResourceServer\Startup.cs*示例下载中。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample11.cs)]

请参阅*AuthorizationServer\ResourceServer\App\_Start\Startup.Auth.cs*示例下载中。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample12.cs)]

请参阅*AuthorizationServer\ResourceServer\App\_Start\Startup.WebApi.cs*示例下载中。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample13.cs)]

- `UseCors` 方法允许所有域的 CORS。
- `UseOAuthBearerAuthentication` 方法使 OAuth 持有者令牌身份验证中间件将接收并验证从授权标头中请求的持有者令牌。
- `Config.SuppressDefaultHostAuthenticaiton` 取消显示默认托管身份验证的主体从该应用程序，因此所有请求都将匿名后此调用。
- `HostAuthenticationFilter` 启用身份验证只为指定的身份验证类型。 在这种情况下，它是持有者身份验证类型。

为了演示已经过身份验证的标识，我们创建 ApiController 以输出当前用户的声明。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample14.cs)]

如果授权服务器和资源服务器不在同一台计算机上，OAuth 中间件将使用不同的计算机密钥来加密和解密持有者访问令牌。 为了共享这两个项目之间的相同私钥，我们添加相同`machinekey`设置中均*web.config*文件。

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample15.xml?highlight=8-10)]

## <a name="create-oauth-20-clients"></a>创建 OAuth 2.0 客户端

 我们使用[DotNetOpenAuth.OAuth2.Client](http://www.nuget.org/packages/DotNetOpenAuth.OAuth2.Client) NuGet 包来简化客户端代码。

### <a name="authorization-code-grant-client"></a>授权代码授予客户端

 此客户端是一个 MVC 应用程序。 它将触发授权代码授予流以从后端获取访问令牌。 它具有单个页面，如下所示：

![](owin-oauth-20-authorization-server/_static/image3.png)

- **Authorize**按钮将浏览器重定向到授权服务器，以通知资源所有者授予对此客户端访问权限。
- **刷新**按钮将获取新访问令牌和刷新令牌中使用当前刷新令牌。
- **访问受保护资源 API**按钮，可以调用资源服务器，以获取当前用户的声明数据并在页面上显示它们。

下面是示例代码的`HomeController`的客户端。

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample16.cs)]

`DotNetOpenAuth` 默认情况下需要 SSL。 由于我们的演示使用 HTTP，因此需要添加以下设置配置文件中：

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample17.xml?highlight=4-6)]

> [!WARNING]
> 安全性-永远不会禁用 SSL 生产应用中。 通过线缆现在以明文发送你的登录凭据。 上面的代码是仅用于本地示例调试和探索。


### <a name="implicit-grant-client"></a>隐式授予客户端

此客户端使用 JavaScript 为：

1. 打开新的窗口和重定向到授权服务器的授权终结点。
2. 从获取访问令牌 URL 片段时它将重定向回。

下图显示了此过程：

![](owin-oauth-20-authorization-server/_static/image4.png)

客户端应具有两个页面： 一个用于主页上，另一个用于回调。下面是示例代码中找到的 JavaScript *Index.cshtml*文件：

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample18.cshtml)]

下面是处理代码中的回调*SignIn.cshtml*文件：

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample19.cshtml)]

> [!NOTE]
> 最佳做法是将 JavaScript 移到外部文件并不将其嵌入和 Razor 标记。 若要使此示例简单明了，已经合并它们。


### <a name="resource-owner-password-credentials-grant-client"></a>资源所有者密码凭据授予客户端

我们使用一个控制台应用程序来演示此客户端。 下面是代码：

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample20.cs)]

### <a name="client-credentials-grant-client"></a>客户端凭据授予客户端

与资源所有者密码凭据授予类似，下面是控制台应用程序代码：

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample21.cs)]
