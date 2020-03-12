---
title: 使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 232a4247f8bea23eec3dc35cba4659c88887124d
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083685"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="ecdeb-102">使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="ecdeb-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="ecdeb-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ecdeb-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="ecdeb-104">本文介绍如何创建使用[Azure Active Directory （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 Blazor WebAssembly 独立应用程序。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="ecdeb-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="ecdeb-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="ecdeb-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="ecdeb-106">Create a tenant</span></span>

<span data-ttu-id="ecdeb-107">按照[教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)中的指导创建 AAD B2C 租户并记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="ecdeb-108">AAD B2C 实例（例如，`https://contoso.b2clogin.com/`，其中包含尾随斜杠）</span><span class="sxs-lookup"><span data-stu-id="ecdeb-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="ecdeb-109">AAD B2C 租户域（例如，`contoso.onmicrosoft.com`）</span><span class="sxs-lookup"><span data-stu-id="ecdeb-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="ecdeb-110">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="ecdeb-110">Register a server API app</span></span>

<span data-ttu-id="ecdeb-111">遵循[教程：在 Azure Active Directory B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**区域注册*服务器 API 应用*的 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="ecdeb-112">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-112">Select **New registration**.</span></span>
1. <span data-ttu-id="ecdeb-113">提供应用的**名称**（例如 **Blazor Server AAD B2C**）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ecdeb-114">对于**支持的帐户类型**，请选择**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="ecdeb-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="ecdeb-115">（多租户）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="ecdeb-116">在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="ecdeb-117">确认**权限** > **向 openid 授予 "管理员以免" 和 "offline_access" 权限**已启用。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ecdeb-118">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-118">Select **Register**.</span></span>

<span data-ttu-id="ecdeb-119">在中**公开 API**：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="ecdeb-120">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="ecdeb-121">选择“保存并继续”。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="ecdeb-122">提供**作用域名称**（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ecdeb-123">提供**管理员同意显示名称**（例如 `Access API`）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="ecdeb-124">提供**管理员同意说明**（例如 `Allows the app to access server app API endpoints.`）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="ecdeb-125">确认 "**状态**" 设置为 "**已启用**"。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="ecdeb-126">选择 "**添加作用域**"。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-126">Select **Add scope**.</span></span>

<span data-ttu-id="ecdeb-127">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-127">Record the following information:</span></span>

* <span data-ttu-id="ecdeb-128">*服务器 API 应用*应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="ecdeb-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="ecdeb-129">目录 ID （租户 ID）（例如 `222222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="ecdeb-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="ecdeb-130">*服务器 API 应用*应用 ID URI （例如 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`，Azure 门户可能会将值默认为客户端 ID）</span><span class="sxs-lookup"><span data-stu-id="ecdeb-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="ecdeb-131">默认作用域（例如 `API.Access`）</span><span class="sxs-lookup"><span data-stu-id="ecdeb-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="ecdeb-132">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="ecdeb-132">Register a client app</span></span>

<span data-ttu-id="ecdeb-133">请按照[教程：将应用程序注册到 Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册*客户端应用*的 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="ecdeb-134">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-134">Select **New registration**.</span></span>
1. <span data-ttu-id="ecdeb-135">提供应用的**名称**（例如， **Blazor 客户端 AAD B2C**）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="ecdeb-136">对于**支持的帐户类型**，请选择**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="ecdeb-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="ecdeb-137">（多租户）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="ecdeb-138">将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="ecdeb-139">确认**权限** > **向 openid 授予 "管理员以免" 和 "offline_access" 权限**已启用。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ecdeb-140">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-140">Select **Register**.</span></span>

<span data-ttu-id="ecdeb-141">在 "**身份验证**" > **平台配置** > **Web**：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="ecdeb-142">确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="ecdeb-143">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="ecdeb-144">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="ecdeb-145">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-145">Select the **Save** button.</span></span>

<span data-ttu-id="ecdeb-146">在 " **API 权限**：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-146">In **API permissions**:</span></span>

1. <span data-ttu-id="ecdeb-147">确认应用程序已**Microsoft Graph** > **用户。读取**权限。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="ecdeb-148">选择 "**添加权限**"，然后选择 **"我的 api"** 。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="ecdeb-149">从 "**名称**" 列中选择 "*服务器 API 应用*" （例如， **Blazor server AAD B2C**"）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ecdeb-150">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-150">Open the **API** list.</span></span>
1. <span data-ttu-id="ecdeb-151">启用对 API 的访问（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ecdeb-152">选择“添加权限”。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="ecdeb-153">选择 "**为 {租户名称} 授予管理内容**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="ecdeb-154">请选择“是”以确认。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="ecdeb-155">在**Home** > **Azure AD B2C** > **用户流**：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="ecdeb-156">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="ecdeb-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="ecdeb-157">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-157">Record the following information:</span></span>

* <span data-ttu-id="ecdeb-158">记录*客户端应用*应用程序 Id （客户端 id）（例如 `33333333-3333-3333-3333-333333333333`）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-158">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="ecdeb-159">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin`）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-159">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="ecdeb-160">创建应用</span><span class="sxs-lookup"><span data-stu-id="ecdeb-160">Create the app</span></span>

<span data-ttu-id="ecdeb-161">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-161">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="ecdeb-162">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-162">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ecdeb-163">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-163">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="ecdeb-164">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="ecdeb-164">Server app configuration</span></span>

<span data-ttu-id="ecdeb-165">*本部分适用于解决方案的**服务器**应用。*</span><span class="sxs-lookup"><span data-stu-id="ecdeb-165">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ecdeb-166">身份验证包</span><span class="sxs-lookup"><span data-stu-id="ecdeb-166">Authentication package</span></span>

<span data-ttu-id="ecdeb-167">`Microsoft.AspNetCore.Authentication.AzureAD.UI`提供对 ASP.NET Core Web Api 的身份验证和授权调用的支持：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-167">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="ecdeb-168">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="ecdeb-168">Authentication service support</span></span>

<span data-ttu-id="ecdeb-169">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-169">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="ecdeb-170">`AddAzureADBearer` 方法在验证 Azure Active Directory 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-170">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="ecdeb-171">`UseAuthentication` 和 `UseAuthorization` 确保：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-171">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="ecdeb-172">应用尝试分析和验证传入请求的令牌。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-172">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="ecdeb-173">任何试图访问受保护资源的请求均不正确。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-173">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="ecdeb-174">应用设置</span><span class="sxs-lookup"><span data-stu-id="ecdeb-174">App settings</span></span>

<span data-ttu-id="ecdeb-175">*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="ecdeb-176">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="ecdeb-176">WeatherForecast controller</span></span>

<span data-ttu-id="ecdeb-177">WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，并将 `[Authorize]` 特性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="ecdeb-178">**务必**要了解：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="ecdeb-179">此 API 控制器中的 `[Authorize]` 属性是保护此 API 不受未经授权的访问的唯一操作。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="ecdeb-180">Blazor WebAssembly 应用程序中使用的 `[Authorize]` 属性仅作为对应用程序的提示，用户应授权该应用程序正常工作。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="ecdeb-181">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="ecdeb-181">Client app configuration</span></span>

<span data-ttu-id="ecdeb-182">*本部分适用于解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="ecdeb-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ecdeb-183">身份验证包</span><span class="sxs-lookup"><span data-stu-id="ecdeb-183">Authentication package</span></span>

<span data-ttu-id="ecdeb-184">创建应用以使用单个 B2C 帐户（`IndividualB2C`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-184">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="ecdeb-185">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ecdeb-186">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="ecdeb-187">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="ecdeb-188">`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="ecdeb-189">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="ecdeb-189">Authentication service support</span></span>

<span data-ttu-id="ecdeb-190">使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="ecdeb-191">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ecdeb-192">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="ecdeb-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="ecdeb-193">`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="ecdeb-194">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="ecdeb-195">Blazor WebAssembly 模板自动配置应用程序，以便为提供给 `dotnet new` 命令（`{APP ID URI}/{DEFAULT SCOPE}`）的默认作用域的安全 API 请求访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-195">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="ecdeb-196">默认访问令牌范围表示访问令牌作用域的列表：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="ecdeb-197">默认情况下，在登录请求中包括。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="ecdeb-198">用于在身份验证后立即设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="ecdeb-199">对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="ecdeb-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="ecdeb-200">可以根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="ecdeb-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="ecdeb-201">索引页</span><span class="sxs-lookup"><span data-stu-id="ecdeb-201">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="ecdeb-202">应用组件</span><span class="sxs-lookup"><span data-stu-id="ecdeb-202">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="ecdeb-203">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="ecdeb-203">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="ecdeb-204">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="ecdeb-204">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="ecdeb-205">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="ecdeb-205">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="ecdeb-206">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="ecdeb-206">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ecdeb-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="ecdeb-207">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="ecdeb-208">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="ecdeb-208">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
