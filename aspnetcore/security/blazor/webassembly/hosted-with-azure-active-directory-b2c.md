---
title: Blazor使用 Azure Active Directory B2C 保护 ASP.NET Core WebAssembly 托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e8b1a1f86becb1e9f0affe14a667253bd0ec16bf
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153661"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="1a8ff-102">Blazor使用 Azure Active Directory B2C 保护 ASP.NET Core WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="1a8ff-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="1a8ff-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1a8ff-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1a8ff-104">本文介绍如何创建 Blazor 使用[AZURE ACTIVE DIRECTORY （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 WebAssembly 独立应用程序。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="1a8ff-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="1a8ff-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="1a8ff-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="1a8ff-106">Create a tenant</span></span>

<span data-ttu-id="1a8ff-107">按照[教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)中的指导创建 AAD B2C 租户并记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="1a8ff-108">AAD B2C 实例（例如， `https://contoso.b2clogin.com/` 包含尾随斜杠）</span><span class="sxs-lookup"><span data-stu-id="1a8ff-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="1a8ff-109">AAD B2C 租户域（例如 `contoso.onmicrosoft.com` ）</span><span class="sxs-lookup"><span data-stu-id="1a8ff-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="1a8ff-110">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="1a8ff-110">Register a server API app</span></span>

<span data-ttu-id="1a8ff-111">遵循[教程：在 Azure Active Directory B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)中的指导，在 Azure 门户的**Azure Active Directory**应用注册区域中注册*服务器 API 应用*的 AAD 应用  >  **App registrations** ：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1a8ff-112">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="1a8ff-112">Select **New registration**.</span></span>
1. <span data-ttu-id="1a8ff-113">提供应用的**名称**（例如， \*\* Blazor 服务器 AAD B2C\*\*）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="1a8ff-114">对于**支持的帐户类型**，请选择**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="1a8ff-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="1a8ff-115">（多租户）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="1a8ff-116">在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="1a8ff-117">确认**权限**"  >  **授予管理员以免到 openid" 和 "offline_access" 权限**已启用。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="1a8ff-118">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-118">Select **Register**.</span></span>

<span data-ttu-id="1a8ff-119">在中**公开 API**：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="1a8ff-120">选择“添加范围”。 </span><span class="sxs-lookup"><span data-stu-id="1a8ff-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="1a8ff-121">选择“保存并继续”。 </span><span class="sxs-lookup"><span data-stu-id="1a8ff-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="1a8ff-122">提供**作用域名称**（例如 `API.Access` ）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1a8ff-123">提供**管理员同意显示名称**（例如 `Access API` ）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="1a8ff-124">提供**管理员同意说明**（例如 `Allows the app to access server app API endpoints.` ）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="1a8ff-125">确认 "**状态**" 设置为 "**已启用**"。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="1a8ff-126">选择“添加作用域”。 </span><span class="sxs-lookup"><span data-stu-id="1a8ff-126">Select **Add scope**.</span></span>

<span data-ttu-id="1a8ff-127">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-127">Record the following information:</span></span>

* <span data-ttu-id="1a8ff-128">*服务器 API 应用*应用程序 ID （客户端 ID）（例如， `11111111-1111-1111-1111-111111111111` ）</span><span class="sxs-lookup"><span data-stu-id="1a8ff-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="1a8ff-129">应用 ID URI （例如，、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` 或提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="1a8ff-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="1a8ff-130">目录 ID （租户 ID）（例如， `222222222-2222-2222-2222-222222222222` ）</span><span class="sxs-lookup"><span data-stu-id="1a8ff-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="1a8ff-131">*服务器 API 应用*应用 ID URI （例如 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` ，Azure 门户可能会将值默认为 "客户端 ID"）</span><span class="sxs-lookup"><span data-stu-id="1a8ff-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="1a8ff-132">默认作用域（例如 `API.Access` ）</span><span class="sxs-lookup"><span data-stu-id="1a8ff-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="1a8ff-133">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="1a8ff-133">Register a client app</span></span>

<span data-ttu-id="1a8ff-134">遵循[教程：将应用程序注册到 Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)中的指导，在 Azure 门户的**Azure Active Directory**应用注册区域中注册*客户端应用*的 AAD 应用  >  **App registrations** ：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1a8ff-135">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="1a8ff-135">Select **New registration**.</span></span>
1. <span data-ttu-id="1a8ff-136">提供应用的**名称**（例如， \*\* Blazor 客户端 AAD B2C\*\*）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="1a8ff-137">对于**支持的帐户类型**，请选择**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="1a8ff-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="1a8ff-138">（多租户）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="1a8ff-139">将 "**重定向 uri** " 下拉状态设置为 " **Web**"，并提供的重定向 uri `https://localhost:5001/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="1a8ff-140">确认**权限**"  >  **授予管理员以免到 openid" 和 "offline_access" 权限**已启用。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="1a8ff-141">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-141">Select **Register**.</span></span>

<span data-ttu-id="1a8ff-142">在 "**身份验证**  >  **平台配置**"  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1a8ff-143">确认存在的**重定向 URI** `https://localhost:5001/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1a8ff-144">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1a8ff-145">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1a8ff-146">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-146">Select the **Save** button.</span></span>

<span data-ttu-id="1a8ff-147">在 " **API 权限**：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-147">In **API permissions**:</span></span>

1. <span data-ttu-id="1a8ff-148">确认应用程序具有**Microsoft Graph**的 "  >  **用户**" 权限。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="1a8ff-149">选择 "**添加权限**"，然后选择 **"我的 api"**。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="1a8ff-150">从 "**名称**" 列中选择 "*服务器 API 应用*" （例如，" \*\* Blazor 服务器 AAD B2C\*\*"）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="1a8ff-151">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-151">Open the **API** list.</span></span>
1. <span data-ttu-id="1a8ff-152">启用对 API 的访问（例如 `API.Access` ）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1a8ff-153">选择“添加权限”  。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="1a8ff-154">选择 "**为 {租户名称} 授予管理内容**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="1a8ff-155">请选择“是”以确认。 </span><span class="sxs-lookup"><span data-stu-id="1a8ff-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="1a8ff-156">在**家庭**  >  **Azure AD B2C**  >  **用户流**：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="1a8ff-157">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="1a8ff-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="1a8ff-158">至少，选择 "**应用程序声明**  >  **显示名称**用户" 属性以 `context.User.Identity.Name` 在组件中填充 `LoginDisplay` （*Shared/LoginDisplay*）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="1a8ff-159">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-159">Record the following information:</span></span>

* <span data-ttu-id="1a8ff-160">记录*客户端应用*应用程序 Id （客户端 id）（例如 `33333333-3333-3333-3333-333333333333` ）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="1a8ff-161">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin` ）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="1a8ff-162">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="1a8ff-162">Create the app</span></span>

<span data-ttu-id="1a8ff-163">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="1a8ff-164">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1a8ff-165">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="1a8ff-166">将应用 ID URI 传递给 `app-id-uri` 选项，但请注意，可能需要在客户端应用中进行配置更改，这在 "[访问令牌范围](#access-token-scopes)" 部分中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="1a8ff-167">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="1a8ff-167">Server app configuration</span></span>

<span data-ttu-id="1a8ff-168">*本部分适用于解决方案的**服务器**应用。*</span><span class="sxs-lookup"><span data-stu-id="1a8ff-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1a8ff-169">身份验证包</span><span class="sxs-lookup"><span data-stu-id="1a8ff-169">Authentication package</span></span>

<span data-ttu-id="1a8ff-170">提供对 ASP.NET Core Web Api 的身份验证和授权的支持是由提供的 `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` ：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="1a8ff-171">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="1a8ff-171">Authentication service support</span></span>

<span data-ttu-id="1a8ff-172">`AddAuthentication`方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认的身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="1a8ff-173">`AddAzureADB2CBearer`方法在验证 Azure Active Directory B2C 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="1a8ff-174">`UseAuthentication`并 `UseAuthorization` 确保：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="1a8ff-175">应用尝试分析和验证传入请求的令牌。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="1a8ff-176">任何试图访问受保护资源的请求均不正确。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="1a8ff-177">用户。 Identity路径名</span><span class="sxs-lookup"><span data-stu-id="1a8ff-177">User.Identity.Name</span></span>

<span data-ttu-id="1a8ff-178">默认情况下， `User.Identity.Name` 不会填充。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="1a8ff-179">若要将应用配置为接收来自 `name` 声明类型的值，请在中配置[TokenValidationParameters](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="1a8ff-180">应用设置</span><span class="sxs-lookup"><span data-stu-id="1a8ff-180">App settings</span></span>

<span data-ttu-id="1a8ff-181">*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="1a8ff-182">示例：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-182">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="1a8ff-183">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="1a8ff-183">WeatherForecast controller</span></span>

<span data-ttu-id="1a8ff-184">WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，该 API 的 `[Authorize]` 属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="1a8ff-185">**务必**要了解：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="1a8ff-186">`[Authorize]`此 api 控制器中的属性只是保护此 api 不受未经授权的访问。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="1a8ff-187">`[Authorize]`WebAssembly 应用程序中使用的属性 Blazor 仅作为对应用程序的提示，用户应授权该应用程序正常工作。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="1a8ff-188">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="1a8ff-188">Client app configuration</span></span>

<span data-ttu-id="1a8ff-189">*本部分适用于解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="1a8ff-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1a8ff-190">身份验证包</span><span class="sxs-lookup"><span data-stu-id="1a8ff-190">Authentication package</span></span>

<span data-ttu-id="1a8ff-191">当创建应用以使用单个 B2C 帐户（ `IndividualB2C` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（）的包引用 `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1a8ff-192">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1a8ff-193">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1a8ff-194">`{VERSION}`将前面的包引用中的替换为 `Microsoft.AspNetCore.Blazor.Templates` 本文中所示的包版本 <xref:blazor/get-started> 。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1a8ff-195">`Microsoft.Authentication.WebAssembly.Msal`包可传递将 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="1a8ff-196">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="1a8ff-196">Authentication service support</span></span>

<span data-ttu-id="1a8ff-197">添加了对实例的支持 `HttpClient` ，其中包括对服务器项目发出请求时的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="1a8ff-198">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="1a8ff-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="1a8ff-199">使用包提供的扩展方法在服务容器中注册对用户进行身份验证的支持 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1a8ff-200">此方法设置应用与 Identity 提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1a8ff-201">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="1a8ff-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="1a8ff-202">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1a8ff-203">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="1a8ff-204">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="1a8ff-205">示例：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="1a8ff-206">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="1a8ff-206">Access token scopes</span></span>

<span data-ttu-id="1a8ff-207">默认访问令牌范围表示访问令牌作用域的列表：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="1a8ff-208">默认情况下，在登录请求中包括。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="1a8ff-209">用于在身份验证后立即设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="1a8ff-210">对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="1a8ff-211">可以根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1a8ff-212">如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1a8ff-213">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1a8ff-214">提供不含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1a8ff-215">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="1a8ff-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1a8ff-216">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="1a8ff-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1a8ff-217">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="1a8ff-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="1a8ff-218">导入文件</span><span class="sxs-lookup"><span data-stu-id="1a8ff-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="1a8ff-219">索引页面</span><span class="sxs-lookup"><span data-stu-id="1a8ff-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="1a8ff-220">应用组件</span><span class="sxs-lookup"><span data-stu-id="1a8ff-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="1a8ff-221">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="1a8ff-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="1a8ff-222">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="1a8ff-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="1a8ff-223">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="1a8ff-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="1a8ff-224">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="1a8ff-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="1a8ff-225">运行应用</span><span class="sxs-lookup"><span data-stu-id="1a8ff-225">Run the app</span></span>

<span data-ttu-id="1a8ff-226">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-226">Run the app from the Server project.</span></span> <span data-ttu-id="1a8ff-227">使用 Visual Studio 时，请在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="1a8ff-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1a8ff-228">其他资源</span><span class="sxs-lookup"><span data-stu-id="1a8ff-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="1a8ff-229">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="1a8ff-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="1a8ff-230">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="1a8ff-230">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="1a8ff-231">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="1a8ff-231">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
