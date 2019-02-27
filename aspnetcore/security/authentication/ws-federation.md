---
title: 使用 WS 联合身份验证在 ASP.NET Core 中的用户进行身份验证
author: chlowell
description: 本教程演示如何在 ASP.NET Core 应用程序使用 WS 联合身份验证。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: 7967410686da0e59742b721c0154e143bf19ba01
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833535"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="98f51-103">使用 WS 联合身份验证在 ASP.NET Core 中的用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="98f51-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="98f51-104">本教程演示如何让用户能够登录使用 WS 联合身份验证提供程序 (如 Active Directory 联合身份验证服务 (ADFS) 或[Azure Active Directory](/azure/active-directory/) (AAD)。</span><span class="sxs-lookup"><span data-stu-id="98f51-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="98f51-105">它使用 ASP.NET Core 2.0 示例应用程序中所述[Facebook、 Google、 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="98f51-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="98f51-106">对于 ASP.NET Core 2.0 应用程序中，WS 联合身份验证的支持由提供[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)。</span><span class="sxs-lookup"><span data-stu-id="98f51-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="98f51-107">此组件从移植[Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)和共享许多该组件的机制。</span><span class="sxs-lookup"><span data-stu-id="98f51-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="98f51-108">然而，组件的几个重要方面有所不同。</span><span class="sxs-lookup"><span data-stu-id="98f51-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="98f51-109">默认情况下，新的中间件：</span><span class="sxs-lookup"><span data-stu-id="98f51-109">By default, the new middleware:</span></span>

* <span data-ttu-id="98f51-110">不允许未经请求的登录名。</span><span class="sxs-lookup"><span data-stu-id="98f51-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="98f51-111">WS 联合身份验证协议的这一功能很容易 XSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="98f51-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="98f51-112">但是，可以使用启用此`AllowUnsolicitedLogins`选项。</span><span class="sxs-lookup"><span data-stu-id="98f51-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="98f51-113">不会检查每个窗体发布的登录消息。</span><span class="sxs-lookup"><span data-stu-id="98f51-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="98f51-114">仅向请求`CallbackPath`将检查是否有登录接`CallbackPath`默认值为`/signin-wsfed`但可以更改通过继承[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性[WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions)类。</span><span class="sxs-lookup"><span data-stu-id="98f51-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="98f51-115">此路径可以与其他身份验证提供程序共享，从而[SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests)选项。</span><span class="sxs-lookup"><span data-stu-id="98f51-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="98f51-116">与 Active Directory 中注册应用程序</span><span class="sxs-lookup"><span data-stu-id="98f51-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="98f51-117">Active Directory 联合身份验证服务</span><span class="sxs-lookup"><span data-stu-id="98f51-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="98f51-118">打开服务器的**添加信赖方信任向导**从 ADFS 管理控制台：</span><span class="sxs-lookup"><span data-stu-id="98f51-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![添加信赖方信任向导：欢迎使用](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="98f51-120">选择手动输入数据：</span><span class="sxs-lookup"><span data-stu-id="98f51-120">Choose to enter data manually:</span></span>

![添加信赖方信任向导：选择数据源](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="98f51-122">输入有关信赖方的显示名称。</span><span class="sxs-lookup"><span data-stu-id="98f51-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="98f51-123">名称并不重要到 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="98f51-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="98f51-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)缺少对令牌加密，因此不配置令牌加密证书的支持：</span><span class="sxs-lookup"><span data-stu-id="98f51-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![添加信赖方信任向导：配置证书](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="98f51-126">启用支持 WS 联合身份验证被动协议，使用应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="98f51-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="98f51-127">确认端口正确，应用程序：</span><span class="sxs-lookup"><span data-stu-id="98f51-127">Verify the port is correct for the app:</span></span>

![添加信赖方信任向导：配置 URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="98f51-129">这必须是 HTTPS URL。</span><span class="sxs-lookup"><span data-stu-id="98f51-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="98f51-130">在开发期间承载应用程序时，IIS Express 可以提供自签名的证书。</span><span class="sxs-lookup"><span data-stu-id="98f51-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="98f51-131">Kestrel 要求手动证书配置。</span><span class="sxs-lookup"><span data-stu-id="98f51-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="98f51-132">请参阅[Kestrel 文档](xref:fundamentals/servers/kestrel)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="98f51-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="98f51-133">单击**下一步**完成向导的其余部分并**关闭**末尾。</span><span class="sxs-lookup"><span data-stu-id="98f51-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="98f51-134">ASP.NET Core 标识需要**名称 ID**声明。</span><span class="sxs-lookup"><span data-stu-id="98f51-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="98f51-135">添加一个从**编辑声明规则**对话框：</span><span class="sxs-lookup"><span data-stu-id="98f51-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![编辑声明规则](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="98f51-137">在中**添加转换声明规则向导**，保留默认**以声明方式发送 LDAP 属性**模板选择，然后单击**下一步**。</span><span class="sxs-lookup"><span data-stu-id="98f51-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="98f51-138">添加规则映射**SAM 帐户名**LDAP 属性到**名称 ID**传出声明：</span><span class="sxs-lookup"><span data-stu-id="98f51-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![添加转换声明规则向导：配置声明规则](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="98f51-140">单击**完成** > **确定**中**编辑声明规则**窗口。</span><span class="sxs-lookup"><span data-stu-id="98f51-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="98f51-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="98f51-141">Azure Active Directory</span></span>

* <span data-ttu-id="98f51-142">导航到 AAD 租户的应用注册边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="98f51-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="98f51-143">单击**新建应用程序注册**:</span><span class="sxs-lookup"><span data-stu-id="98f51-143">Click **New application registration**:</span></span>

![Azure Active Directory:应用注册](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="98f51-145">输入应用注册的名称。</span><span class="sxs-lookup"><span data-stu-id="98f51-145">Enter a name for the app registration.</span></span> <span data-ttu-id="98f51-146">这并不重要到 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="98f51-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="98f51-147">输入应用程序以侦听的 URL**单一登录 URL**:</span><span class="sxs-lookup"><span data-stu-id="98f51-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory:创建应用程序注册](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="98f51-149">单击**终结点**并记下**联合身份验证元数据文档**URL。</span><span class="sxs-lookup"><span data-stu-id="98f51-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="98f51-150">这是 WS 联合身份验证中间件的`MetadataAddress`:</span><span class="sxs-lookup"><span data-stu-id="98f51-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory:终结点](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="98f51-152">导航到新的应用注册。</span><span class="sxs-lookup"><span data-stu-id="98f51-152">Navigate to the new app registration.</span></span> <span data-ttu-id="98f51-153">单击**设置** > **属性**并记下**应用程序 ID URI**。</span><span class="sxs-lookup"><span data-stu-id="98f51-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="98f51-154">这是 WS 联合身份验证中间件的`Wtrealm`:</span><span class="sxs-lookup"><span data-stu-id="98f51-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory:应用程序注册属性](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="98f51-156">为 ASP.NET Core 标识的外部登录提供程序中添加 WS 联合身份验证</span><span class="sxs-lookup"><span data-stu-id="98f51-156">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="98f51-157">添加一个依赖项[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)到项目。</span><span class="sxs-lookup"><span data-stu-id="98f51-157">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="98f51-158">添加 WS 联合身份验证到`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="98f51-158">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddIdentity<IdentityUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddAuthentication()
        .AddWsFederation(options =>
        {
            // MetadataAddress represents the Active Directory instance used to authenticate users.
            options.MetadataAddress = "https://<ADFS FQDN or AAD tenant>/FederationMetadata/2007-06/FederationMetadata.xml";

            // Wtrealm is the app's identifier in the Active Directory instance.
            // For ADFS, use the relying party's identifier, its WS-Federation Passive protocol URL:
            options.Wtrealm = "https://localhost:44307/";

            // For AAD, use the App ID URI from the app registration's Properties blade:
            options.Wtrealm = "https://wsfedsample.onmicrosoft.com/bf0e7e6d-056e-4e37-b9a6-2c36797b9f01";
        });

    services.AddMvc()
     // ...
    ```

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="98f51-159">使用 WS 联合身份验证登录</span><span class="sxs-lookup"><span data-stu-id="98f51-159">Log in with WS-Federation</span></span>

<span data-ttu-id="98f51-160">浏览到应用程序并单击**登录**nav 标头中的链接。</span><span class="sxs-lookup"><span data-stu-id="98f51-160">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="98f51-161">提供了一个选项，能够使用 WsFederation 进行登录：![登录页](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="98f51-161">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="98f51-162">使用 ADFS 作为提供程序，该按钮将重定向到 ADFS 登录页：![ADFS 登录页](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="98f51-162">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="98f51-163">使用 Azure Active Directory 作为提供程序，该按钮将重定向到 AAD 登录页：![AAD 登录页](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="98f51-163">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="98f51-164">在成功登录的新用户重定向到应用的用户注册页：![注册页](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="98f51-164">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="98f51-165">使用 WS 联合身份验证而无需 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="98f51-165">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="98f51-166">没有标识，可以使用 WS 联合身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="98f51-166">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="98f51-167">例如：</span><span class="sxs-lookup"><span data-stu-id="98f51-167">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = WsFederationDefaults.AuthenticationScheme;
    })
    .AddWsFederation(options =>
    {
        options.Wtrealm = Configuration["wsfed:realm"];
        options.MetadataAddress = Configuration["wsfed:metadata"];
    })
    .AddCookie();
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
        // …
}
```
