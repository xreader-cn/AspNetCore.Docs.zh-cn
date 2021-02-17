---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552696"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a><span data-ttu-id="05220-101">禁用默认帐户验证</span><span class="sxs-lookup"><span data-stu-id="05220-101">Disable default account verification</span></span>

<span data-ttu-id="05220-102">使用默认模板时，会将用户重定向到用户 `Account.RegisterConfirmation` 可以在其中选择要确认帐户的链接。</span><span class="sxs-lookup"><span data-stu-id="05220-102">With the default templates, the user is redirected to the `Account.RegisterConfirmation` where they can select a link to have the account confirmed.</span></span> <span data-ttu-id="05220-103">默认值 `Account.RegisterConfirmation` ***仅*** 用于测试，应在生产应用中禁用自动帐户验证。</span><span class="sxs-lookup"><span data-stu-id="05220-103">The default `Account.RegisterConfirmation` is used ***only*** for testing, automatic account verification should be disabled in a production app.</span></span>

<span data-ttu-id="05220-104">若要在注册时要求确认帐户并阻止立即登录，请 `DisplayConfirmAccountLink = false` 在 */Areas/ Identity /Pages/Account/RegisterConfirmation.cshtml.cs* 中设置：</span><span class="sxs-lookup"><span data-stu-id="05220-104">To require a confirmed account and prevent immediate login at registration, set `DisplayConfirmAccountLink = false` in */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*:</span></span>

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]