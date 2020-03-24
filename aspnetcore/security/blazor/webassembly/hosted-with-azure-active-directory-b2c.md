---
title: 使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 0083f179f85371d4751fb179194417681fc1a01d
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219059"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="92bf7-102">使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="92bf7-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="92bf7-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="92bf7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="92bf7-104">本文介绍如何创建使用[Azure Active Directory （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 Blazor WebAssembly 独立应用程序。</span><span class="sxs-lookup"><span data-stu-id="92bf7-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="92bf7-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="92bf7-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="92bf7-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="92bf7-106">Create a tenant</span></span>

<span data-ttu-id="92bf7-107">按照[教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)中的指导创建 AAD B2C 租户并记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="92bf7-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="92bf7-108">AAD B2C 实例（例如，`https://contoso.b2clogin.com/`，其中包含尾随斜杠）</span><span class="sxs-lookup"><span data-stu-id="92bf7-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="92bf7-109">AAD B2C 租户域（例如，`contoso.onmicrosoft.com`）</span><span class="sxs-lookup"><span data-stu-id="92bf7-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="92bf7-110">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="92bf7-110">Register a server API app</span></span>

<span data-ttu-id="92bf7-111">遵循[教程：在 Azure Active Directory B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**区域注册*服务器 API 应用*的 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="92bf7-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="92bf7-112">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="92bf7-112">Select **New registration**.</span></span>
1. <span data-ttu-id="92bf7-113">提供应用的**名称**（例如 **Blazor Server AAD B2C**）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="92bf7-114">对于**支持的帐户类型**，请选择**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="92bf7-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="92bf7-115">（多租户）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="92bf7-116">在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。</span><span class="sxs-lookup"><span data-stu-id="92bf7-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="92bf7-117">确认**权限** > **向 openid 授予 "管理员以免" 和 "offline_access" 权限**已启用。</span><span class="sxs-lookup"><span data-stu-id="92bf7-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="92bf7-118">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="92bf7-118">Select **Register**.</span></span>

<span data-ttu-id="92bf7-119">在中**公开 API**：</span><span class="sxs-lookup"><span data-stu-id="92bf7-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="92bf7-120">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="92bf7-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="92bf7-121">选择“保存并继续”。</span><span class="sxs-lookup"><span data-stu-id="92bf7-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="92bf7-122">提供**作用域名称**（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="92bf7-123">提供**管理员同意显示名称**（例如 `Access API`）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="92bf7-124">提供**管理员同意说明**（例如 `Allows the app to access server app API endpoints.`）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="92bf7-125">确认 "**状态**" 设置为 "**已启用**"。</span><span class="sxs-lookup"><span data-stu-id="92bf7-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="92bf7-126">选择 "**添加作用域**"。</span><span class="sxs-lookup"><span data-stu-id="92bf7-126">Select **Add scope**.</span></span>

<span data-ttu-id="92bf7-127">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="92bf7-127">Record the following information:</span></span>

* <span data-ttu-id="92bf7-128">*服务器 API 应用*应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="92bf7-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="92bf7-129">目录 ID （租户 ID）（例如 `222222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="92bf7-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="92bf7-130">*服务器 API 应用*应用 ID URI （例如 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`，Azure 门户可能会将值默认为客户端 ID）</span><span class="sxs-lookup"><span data-stu-id="92bf7-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="92bf7-131">默认作用域（例如 `API.Access`）</span><span class="sxs-lookup"><span data-stu-id="92bf7-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="92bf7-132">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="92bf7-132">Register a client app</span></span>

<span data-ttu-id="92bf7-133">请按照[教程：将应用程序注册到 Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册*客户端应用*的 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="92bf7-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="92bf7-134">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="92bf7-134">Select **New registration**.</span></span>
1. <span data-ttu-id="92bf7-135">提供应用的**名称**（例如， **Blazor 客户端 AAD B2C**）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="92bf7-136">对于**支持的帐户类型**，请选择**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="92bf7-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="92bf7-137">（多租户）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="92bf7-138">将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="92bf7-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="92bf7-139">确认**权限** > **向 openid 授予 "管理员以免" 和 "offline_access" 权限**已启用。</span><span class="sxs-lookup"><span data-stu-id="92bf7-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="92bf7-140">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="92bf7-140">Select **Register**.</span></span>

<span data-ttu-id="92bf7-141">在 "**身份验证**" > **平台配置** > **Web**：</span><span class="sxs-lookup"><span data-stu-id="92bf7-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="92bf7-142">确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="92bf7-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="92bf7-143">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="92bf7-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="92bf7-144">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="92bf7-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="92bf7-145">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="92bf7-145">Select the **Save** button.</span></span>

<span data-ttu-id="92bf7-146">在 " **API 权限**：</span><span class="sxs-lookup"><span data-stu-id="92bf7-146">In **API permissions**:</span></span>

1. <span data-ttu-id="92bf7-147">确认应用程序已**Microsoft Graph** > **用户。读取**权限。</span><span class="sxs-lookup"><span data-stu-id="92bf7-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="92bf7-148">选择 "**添加权限**"，然后选择 **"我的 api"** 。</span><span class="sxs-lookup"><span data-stu-id="92bf7-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="92bf7-149">从 "**名称**" 列中选择 "*服务器 API 应用*" （例如， **Blazor server AAD B2C**"）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="92bf7-150">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="92bf7-150">Open the **API** list.</span></span>
1. <span data-ttu-id="92bf7-151">启用对 API 的访问（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="92bf7-152">选择“添加权限”。</span><span class="sxs-lookup"><span data-stu-id="92bf7-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="92bf7-153">选择 "**为 {租户名称} 授予管理内容**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="92bf7-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="92bf7-154">请选择“是”以确认。</span><span class="sxs-lookup"><span data-stu-id="92bf7-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="92bf7-155">在**Home** > **Azure AD B2C** > **用户流**：</span><span class="sxs-lookup"><span data-stu-id="92bf7-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="92bf7-156">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="92bf7-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="92bf7-157">至少，选择 "**应用程序声明**" > "**显示名称**用户属性"，以填充 `LoginDisplay` 组件中的 `context.User.Identity.Name` （*Shared/LoginDisplay*）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-157">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="92bf7-158">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="92bf7-158">Record the following information:</span></span>

* <span data-ttu-id="92bf7-159">记录*客户端应用*应用程序 Id （客户端 id）（例如 `33333333-3333-3333-3333-333333333333`）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-159">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="92bf7-160">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin`）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-160">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="92bf7-161">创建应用</span><span class="sxs-lookup"><span data-stu-id="92bf7-161">Create the app</span></span>

<span data-ttu-id="92bf7-162">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="92bf7-162">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="92bf7-163">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="92bf7-163">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="92bf7-164">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="92bf7-164">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="92bf7-165">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="92bf7-165">Server app configuration</span></span>

<span data-ttu-id="92bf7-166">*本部分适用于解决方案的**服务器**应用。*</span><span class="sxs-lookup"><span data-stu-id="92bf7-166">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="92bf7-167">身份验证包</span><span class="sxs-lookup"><span data-stu-id="92bf7-167">Authentication package</span></span>

<span data-ttu-id="92bf7-168">`Microsoft.AspNetCore.Authentication.AzureAD.UI`提供对 ASP.NET Core Web Api 的身份验证和授权调用的支持：</span><span class="sxs-lookup"><span data-stu-id="92bf7-168">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="92bf7-169">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="92bf7-169">Authentication service support</span></span>

<span data-ttu-id="92bf7-170">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="92bf7-170">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="92bf7-171">`AddAzureADBearer` 方法在验证 Azure Active Directory 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：</span><span class="sxs-lookup"><span data-stu-id="92bf7-171">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="92bf7-172">`UseAuthentication` 和 `UseAuthorization` 确保：</span><span class="sxs-lookup"><span data-stu-id="92bf7-172">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="92bf7-173">应用尝试分析和验证传入请求的令牌。</span><span class="sxs-lookup"><span data-stu-id="92bf7-173">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="92bf7-174">任何试图访问受保护资源的请求均不正确。</span><span class="sxs-lookup"><span data-stu-id="92bf7-174">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="92bf7-175">应用设置</span><span class="sxs-lookup"><span data-stu-id="92bf7-175">App settings</span></span>

<span data-ttu-id="92bf7-176">*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项。</span><span class="sxs-lookup"><span data-stu-id="92bf7-176">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="92bf7-177">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="92bf7-177">WeatherForecast controller</span></span>

<span data-ttu-id="92bf7-178">WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，并将 `[Authorize]` 特性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="92bf7-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="92bf7-179">**务必**要了解：</span><span class="sxs-lookup"><span data-stu-id="92bf7-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="92bf7-180">此 API 控制器中的 `[Authorize]` 属性是保护此 API 不受未经授权的访问的唯一操作。</span><span class="sxs-lookup"><span data-stu-id="92bf7-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="92bf7-181">Blazor WebAssembly 应用程序中使用的 `[Authorize]` 属性仅作为对应用程序的提示，用户应授权该应用程序正常工作。</span><span class="sxs-lookup"><span data-stu-id="92bf7-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="92bf7-182">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="92bf7-182">Client app configuration</span></span>

<span data-ttu-id="92bf7-183">*本部分适用于解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="92bf7-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="92bf7-184">身份验证包</span><span class="sxs-lookup"><span data-stu-id="92bf7-184">Authentication package</span></span>

<span data-ttu-id="92bf7-185">创建应用以使用单个 B2C 帐户（`IndividualB2C`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="92bf7-185">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="92bf7-186">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="92bf7-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="92bf7-187">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="92bf7-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="92bf7-188">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="92bf7-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="92bf7-189">`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。</span><span class="sxs-lookup"><span data-stu-id="92bf7-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="92bf7-190">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="92bf7-190">Authentication service support</span></span>

<span data-ttu-id="92bf7-191">使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="92bf7-191">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="92bf7-192">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="92bf7-192">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="92bf7-193">Program.cs：</span><span class="sxs-lookup"><span data-stu-id="92bf7-193">*Program.cs*:</span></span>

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

<span data-ttu-id="92bf7-194">`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="92bf7-194">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="92bf7-195">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="92bf7-195">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="92bf7-196">Blazor WebAssembly 模板自动配置应用程序，以便为提供给 `dotnet new` 命令（`{APP ID URI}/{DEFAULT SCOPE}`）的默认作用域的安全 API 请求访问令牌。</span><span class="sxs-lookup"><span data-stu-id="92bf7-196">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="92bf7-197">默认访问令牌范围表示访问令牌作用域的列表：</span><span class="sxs-lookup"><span data-stu-id="92bf7-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="92bf7-198">默认情况下，在登录请求中包括。</span><span class="sxs-lookup"><span data-stu-id="92bf7-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="92bf7-199">用于在身份验证后立即设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="92bf7-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="92bf7-200">对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="92bf7-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="92bf7-201">可以根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="92bf7-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="92bf7-202">索引页面</span><span class="sxs-lookup"><span data-stu-id="92bf7-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="92bf7-203">应用组件</span><span class="sxs-lookup"><span data-stu-id="92bf7-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="92bf7-204">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="92bf7-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="92bf7-205">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="92bf7-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="92bf7-206">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="92bf7-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="92bf7-207">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="92bf7-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="92bf7-208">运行应用</span><span class="sxs-lookup"><span data-stu-id="92bf7-208">Run the app</span></span>

<span data-ttu-id="92bf7-209">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="92bf7-209">Run the app from the Server project.</span></span> <span data-ttu-id="92bf7-210">使用 Visual Studio 时，请在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="92bf7-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="92bf7-211">其他资源</span><span class="sxs-lookup"><span data-stu-id="92bf7-211">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="92bf7-212">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="92bf7-212">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
