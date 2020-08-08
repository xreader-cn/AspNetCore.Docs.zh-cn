---
title: 在 ASP.NET Core 中用 WS 联合身份验证用户
author: chlowell
description: 本教程演示如何在 ASP.NET Core 的应用程序中使用 WS 联合身份验证。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: e303679190a7d7f42d8525541cec031ba090fd7a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022298"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="8c9fe-103">在 ASP.NET Core 中用 WS 联合身份验证用户</span><span class="sxs-lookup"><span data-stu-id="8c9fe-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="8c9fe-104">本教程演示如何使用户能够使用 WS 联合身份验证提供程序（如 Active Directory 联合身份验证服务 (ADFS) 或[Azure Active Directory](/azure/active-directory/) (AAD) ）登录。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="8c9fe-105">它使用[Facebook、Google 和 external 提供程序身份验证](xref:security/authentication/social/index)中介绍的 ASP.NET Core 示例应用。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="8c9fe-106">对于 ASP.NET Core 的应用程序，WS 联合身份验证支持由[WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)提供。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="8c9fe-107">此组件从[Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)移植，并共享该组件的许多机制。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="8c9fe-108">不过，这些组件在一些重要的方面有所不同。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="8c9fe-109">默认情况下，新的中间件：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-109">By default, the new middleware:</span></span>

* <span data-ttu-id="8c9fe-110">不允许未经请求的登录。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="8c9fe-111">WS 联合身份验证协议的此功能容易受到 XSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="8c9fe-112">但是，可以通过 `AllowUnsolicitedLogins` 选项启用。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="8c9fe-113">不会检查每个窗体张贴内容中的登录消息。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="8c9fe-114">仅检查对的请求 `CallbackPath` 。 `CallbackPath` 默认为， `/signin-wsfed` 但可以通过[WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性更改为。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="8c9fe-115">通过启用[SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests)选项，可以将此路径与其他身份验证提供程序共享。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="8c9fe-116">将应用注册到 Active Directory</span><span class="sxs-lookup"><span data-stu-id="8c9fe-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="8c9fe-117">Active Directory 联合身份验证服务</span><span class="sxs-lookup"><span data-stu-id="8c9fe-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="8c9fe-118">从 ADFS 管理控制台打开服务器的 "**添加信赖方信任向导**"：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![添加信赖方信任向导：欢迎](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="8c9fe-120">选择手动输入数据：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-120">Choose to enter data manually:</span></span>

![添加信赖方信任向导：选择数据源](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="8c9fe-122">为信赖方输入显示名称。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="8c9fe-123">该名称对 ASP.NET Core 应用并不重要。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="8c9fe-124">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)不支持令牌加密，因此请不要配置令牌加密证书：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![添加信赖方信任向导：配置证书](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="8c9fe-126">使用应用的 URL 启用对 WS 联合身份验证被动协议的支持。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="8c9fe-127">验证该应用的端口是否正确：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-127">Verify the port is correct for the app:</span></span>

![添加信赖方信任向导：配置 URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="8c9fe-129">这必须是 HTTPS URL。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="8c9fe-130">IIS Express 可以在开发过程中托管应用时提供自签名证书。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="8c9fe-131">Kestrel 需要手动配置证书。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="8c9fe-132">有关更多详细信息，请参阅[Kestrel 文档](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="8c9fe-133">在向导的其余部分中单击 "**下一步**"，然后**关闭**。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="8c9fe-134">ASP.NET Core Identity 需要**名称 ID**声明。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="8c9fe-135">从 "**编辑声明规则**" 对话框中添加一个：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![编辑声明规则](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="8c9fe-137">在 "**添加转换声明规则向导**" 中，保留 "默认**发送 LDAP 属性作为声明**" 模板，然后单击 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="8c9fe-138">添加一个规则，将**SAM 帐户名称**LDAP 属性映射到**名称 ID**传出声明：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![添加转换声明规则向导：配置声明规则](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="8c9fe-140">单击**Finish**  >  "**编辑声明规则**" 窗口中的 **"完成"** 。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="8c9fe-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="8c9fe-141">Azure Active Directory</span></span>

* <span data-ttu-id="8c9fe-142">导航到 AAD 租户的 "应用注册" 边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="8c9fe-143">单击 "**新应用程序注册**"：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-143">Click **New application registration**:</span></span>

![Azure Active Directory：应用注册](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="8c9fe-145">输入应用注册的名称。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-145">Enter a name for the app registration.</span></span> <span data-ttu-id="8c9fe-146">这对于 ASP.NET Core 应用并不重要。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="8c9fe-147">输入应用作为**登录 url**侦听的 url：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory：创建应用注册](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="8c9fe-149">单击 "**终结点**" 并记下 "**联合元数据文档**" URL。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="8c9fe-150">这是 WS 联合身份验证中间件的 `MetadataAddress` ：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory：终结点](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="8c9fe-152">导航到新的应用注册。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-152">Navigate to the new app registration.</span></span> <span data-ttu-id="8c9fe-153">单击 "**公开 API**"。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-153">Click **Expose an API**.</span></span> <span data-ttu-id="8c9fe-154">单击 "应用程序 ID URI**集**  >  **保存**"。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-154">Click Application ID URI **Set** > **Save**.</span></span> <span data-ttu-id="8c9fe-155">记下**应用程序 ID URI**。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-155">Make note of the  **Application ID URI**.</span></span> <span data-ttu-id="8c9fe-156">这是 WS 联合身份验证中间件的 `Wtrealm` ：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-156">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory：应用注册属性](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-no-locidentity"></a><span data-ttu-id="8c9fe-158">使用不带 ASP.NET Core 的 WS 联合身份验证Identity</span><span class="sxs-lookup"><span data-stu-id="8c9fe-158">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="8c9fe-159">无需使用 WS 联合身份验证中间件 Identity 。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-159">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="8c9fe-160">例如：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-160">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-no-locidentity"></a><span data-ttu-id="8c9fe-161">添加 WS-FEDERATION 作为 ASP.NET Core 的外部登录提供程序Identity</span><span class="sxs-lookup"><span data-stu-id="8c9fe-161">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="8c9fe-162">将[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)上的依赖项添加到项目。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-162">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="8c9fe-163">将 WS 联合身份验证添加到 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="8c9fe-163">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="8c9fe-164">用 WS 联合身份验证登录</span><span class="sxs-lookup"><span data-stu-id="8c9fe-164">Log in with WS-Federation</span></span>

<span data-ttu-id="8c9fe-165">浏览到应用，并单击导航头中的 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="8c9fe-165">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="8c9fe-166">有一个选项可以使用 WsFederation： ![ 登录页登录](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="8c9fe-166">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="8c9fe-167">使用 ADFS 作为提供程序时，按钮会重定向到 ADFS 登录页： ![ adfs 登录页](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="8c9fe-167">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="8c9fe-168">使用 Azure Active Directory 作为提供程序时，该按钮将重定向到 AAD 登录页： ![ aad 登录页](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="8c9fe-168">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="8c9fe-169">成功登录新用户重定向到应用的用户注册页面： ![ 注册页面](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="8c9fe-169">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
