---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: 使用 Web API 中的 SSL |Microsoft Docs
author: MikeWasson
description: 演示如何使用 SSL 和 ASP.NET Web API，包括使用 SSL 客户端证书。
ms.author: riande
ms.date: 02/22/2019
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 69c0d217f605096d968435c062ee9931f8dff75f
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744100"
---
<a name="working-with-ssl-in-web-api"></a>使用 Web API 中的 SSL
====================
通过[Mike Wasson](https://github.com/MikeWasson)

通过一般 HTTP 几个常见的身份验证方案不安全。 具体而言，基本身份验证和窗体身份验证发送未加密的凭据。 若要为安全的这些身份验证方案*必须*使用 SSL。 此外，可以使用 SSL 客户端证书进行身份验证的客户端。

## <a name="enabling-ssl-on-the-server"></a>在服务器上启用 SSL

若要设置 SSL 在 IIS 7 或更高版本：

- 创建或获取证书。 对于测试，可以创建自签名的证书。
- 添加 HTTPS 绑定。

有关详细信息，请参阅[IIS 7 上设置 SSL 如何](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis)。

对于本地测试，可以启用在 IIS Express 从 Visual Studio 中的 SSL。 在属性窗口中设置**已启用 SSL**到**True**。 记下的值**SSL URL**; 将此 URL 用于测试 HTTPS 连接。

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a>Web API 控制器中强制 SSL

如果必须使用 HTTPS 和 HTTP 绑定，客户端仍可以使用 HTTP 来访问此站点。 可能会使一些资源，以可通过 HTTP，而其他资源需要 SSL。 在这种情况下，使用操作筛选器的受保护的资源要求 SSL。 下面的代码显示了检查 SSL 的 Web API 身份验证筛选器：

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

将此筛选器添加到任何要求使用 SSL 的 Web API 操作：

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a>SSL 客户端证书

SSL 通过使用公钥基础结构证书提供身份验证。 服务器必须提供对服务器到客户端进行身份验证的证书。 不太常见的客户端提供证书提供给服务器，但这是用于身份验证客户端的一个选项。 若要使用具有 SSL 的客户端证书，需要一种分发给用户的签名的证书。 对于许多应用程序类型，这并不是良好的用户体验，但在某些环境 （例如，适用于企业），它可能是一种可行。

| 优点 | 缺点 |
| --- | --- |
| -证书凭据是强于用户名/密码。 SSL 提供了一个完整的安全通道，通过身份验证、 消息完整性和消息加密。 | -你必须获取并管理 PKI 证书。 客户端平台必须支持 SSL 客户端证书。 |

若要配置 IIS 以接受客户端证书，请打开 IIS 管理器，并执行以下步骤：

1. 单击树视图中的站点节点。
2. 双击**SSL 设置**在中间窗格中的功能。
3. 下**客户端证书**，选择以下选项之一： 

    - **接受**:IIS 将接受来自客户端的证书，但不要求。
    - **需要**:需要客户端证书。 （若要启用此选项，您还必须选择"要求 SSL"）

此外可以在 ApplicationHost.config 文件中设置这些选项：

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

**SslNegotiateCert**标记表示 IIS 将接受来自客户端的证书，但不需要进行一次 （等效于在 IIS 管理器的"接受"选项）。 如果需要证书，请设置**SslRequireCert**标志。 用于测试，您可以还中设置这些选项在 IIS Express 中本地 applicationhost。配置文件中，位于"Documents\IISExpress\config"。

### <a name="creating-a-client-certificate-for-testing"></a>创建客户端证书以便进行测试

出于测试目的，可以使用[MakeCert.exe](/windows/desktop/SecCrypto/makecert)创建客户端证书。 首先，创建测试根证书颁发机构：

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

Makecert 将提示你输入的私钥的密码。

接下来，将证书添加到测试服务器的"受信任的根证书颁发机构"存储，按如下所示：

1. 打开 MMC。
2. 下**文件**，选择**添加/删除管理单元中**。
3. 选择**计算机帐户**。
4. 选择**本地计算机**并完成向导。
5. 在导航窗格中，展开"受信任的根证书颁发机构"节点。
6. 上**操作**菜单，依次指向**的所有任务**，然后单击**导入**以启动证书导入向导。
7. 浏览到证书文件，TempCA.cer。
8. 单击**开放**，然后单击**下一步**并完成向导。 （将提示您重新输入密码。）

现在，创建第一个证书由签名的客户端证书：

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a>Web API 中使用客户端证书

在服务器端，您可以通过调用中获取的客户端证书[GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx)请求消息。 该方法返回 null，如果没有客户端证书。 否则，它将返回**X509Certificate2**实例。 使用此对象以获取从证书颁发者和使用者等信息。 然后您可以使用此信息用于身份验证和/或授权。

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]
