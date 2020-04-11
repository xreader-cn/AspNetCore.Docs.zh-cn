---
title: 使用 AzureBlazor活动目录 B2C 保护 ASP.NET核心 Web 组件托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123473"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="da38c-102">使用 AzureBlazor活动目录 B2C 保护 ASP.NET核心 Web 组件托管应用</span><span class="sxs-lookup"><span data-stu-id="da38c-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="da38c-103">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="da38c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="da38c-104">本文介绍如何创建使用 Azure Blazor [活动目录 （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 WebAssembly 独立应用。</span><span class="sxs-lookup"><span data-stu-id="da38c-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="da38c-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="da38c-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="da38c-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="da38c-106">Create a tenant</span></span>

<span data-ttu-id="da38c-107">按照教程中的指南[：创建 Azure 活动目录 B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)以创建 AAD B2C 租户并记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="da38c-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="da38c-108">AAD B2C 实例（例如`https://contoso.b2clogin.com/`，包括尾随斜杠）</span><span class="sxs-lookup"><span data-stu-id="da38c-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="da38c-109">AAD B2C 租户域（例如`contoso.onmicrosoft.com`，</span><span class="sxs-lookup"><span data-stu-id="da38c-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="da38c-110">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="da38c-110">Register a server API app</span></span>

<span data-ttu-id="da38c-111">按照教程中的指南[：在 Azure 活动目录 B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)，在 Azure 门户的 Azure**活动目录** > **应用注册**区域注册*服务器 API 应用*的 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="da38c-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="da38c-112">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="da38c-112">Select **New registration**.</span></span>
1. <span data-ttu-id="da38c-113">为应用提供**名称**（例如，**Blazor服务器 AAD B2C**）。</span><span class="sxs-lookup"><span data-stu-id="da38c-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="da38c-114">对于**支持的帐户类型**，选择**任何组织目录中的帐户或任何标识提供程序。用于使用 Azure AD B2C 对用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="da38c-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="da38c-115">（多租户）用于此体验。</span><span class="sxs-lookup"><span data-stu-id="da38c-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="da38c-116">在这种情况下，*服务器 API 应用*不需要重定向**URI，** 因此请将下拉列表设置为**Web，** 并且不输入重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da38c-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="da38c-117">确认**启用了"权限** > **授予"管理员集中打开 id 和offline_access权限**。</span><span class="sxs-lookup"><span data-stu-id="da38c-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="da38c-118">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="da38c-118">Select **Register**.</span></span>

<span data-ttu-id="da38c-119">在**公开 API 中**：</span><span class="sxs-lookup"><span data-stu-id="da38c-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="da38c-120">选择 **"添加范围**"。</span><span class="sxs-lookup"><span data-stu-id="da38c-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="da38c-121">选择“保存并继续”。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="da38c-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="da38c-122">提供**范围名称**（例如， `API.Access`</span><span class="sxs-lookup"><span data-stu-id="da38c-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="da38c-123">提供**管理员同意显示名称**（例如， `Access API`</span><span class="sxs-lookup"><span data-stu-id="da38c-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="da38c-124">提供**管理员同意说明**（例如， `Allows the app to access server app API endpoints.`</span><span class="sxs-lookup"><span data-stu-id="da38c-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="da38c-125">确认**状态**设置为**启用**。</span><span class="sxs-lookup"><span data-stu-id="da38c-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="da38c-126">选择 **"添加范围**"。</span><span class="sxs-lookup"><span data-stu-id="da38c-126">Select **Add scope**.</span></span>

<span data-ttu-id="da38c-127">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="da38c-127">Record the following information:</span></span>

* <span data-ttu-id="da38c-128">*服务器 API 应用*应用程序 ID（客户端 ID）（例如`11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="da38c-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="da38c-129">应用 ID URI（例如`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`，`api://11111111-1111-1111-1111-111111111111`或您提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="da38c-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="da38c-130">目录 ID（租户 ID）（例如`222222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="da38c-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="da38c-131">*服务器 API 应用*应用 ID URI（例如`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`，Azure 门户可能会将该值默认为客户端 ID）</span><span class="sxs-lookup"><span data-stu-id="da38c-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="da38c-132">默认作用域（例如）， `API.Access`</span><span class="sxs-lookup"><span data-stu-id="da38c-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="da38c-133">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="da38c-133">Register a client app</span></span>

<span data-ttu-id="da38c-134">按照教程中的指南[：再次在 Azure 活动目录 B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)，在 Azure 门户的**Azure 活动目录** > **应用注册**区域为*客户端应用*注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="da38c-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="da38c-135">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="da38c-135">Select **New registration**.</span></span>
1. <span data-ttu-id="da38c-136">为应用提供**名称**（例如，**Blazor客户端 AAD B2C**）。</span><span class="sxs-lookup"><span data-stu-id="da38c-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="da38c-137">对于**支持的帐户类型**，选择**任何组织目录中的帐户或任何标识提供程序。用于使用 Azure AD B2C 对用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="da38c-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="da38c-138">（多租户）用于此体验。</span><span class="sxs-lookup"><span data-stu-id="da38c-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="da38c-139">将**重定向 URI**下拉列表设置为**Web，** 并提供 重定向 URI。 `https://localhost:5001/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="da38c-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="da38c-140">确认**启用了"权限** > **授予"管理员集中打开 id 和offline_access权限**。</span><span class="sxs-lookup"><span data-stu-id="da38c-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="da38c-141">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="da38c-141">Select **Register**.</span></span>

<span data-ttu-id="da38c-142">在**身份验证** > **平台配置中** > **，Web**：</span><span class="sxs-lookup"><span data-stu-id="da38c-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="da38c-143">确认存在 重定向`https://localhost:5001/authentication/login-callback` **URI。**</span><span class="sxs-lookup"><span data-stu-id="da38c-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="da38c-144">对于**隐式授予**，选择 Access**令牌**和**ID 令牌的**复选框。</span><span class="sxs-lookup"><span data-stu-id="da38c-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="da38c-145">此体验可以接受应用的剩余默认值。</span><span class="sxs-lookup"><span data-stu-id="da38c-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="da38c-146">选择"**保存**"按钮。</span><span class="sxs-lookup"><span data-stu-id="da38c-146">Select the **Save** button.</span></span>

<span data-ttu-id="da38c-147">在**API 权限**中：</span><span class="sxs-lookup"><span data-stu-id="da38c-147">In **API permissions**:</span></span>

1. <span data-ttu-id="da38c-148">确认应用具有**Microsoft 图形** > **用户.读取**权限。</span><span class="sxs-lookup"><span data-stu-id="da38c-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="da38c-149">选择 **"添加"** 后跟**我的 API 的权限**。</span><span class="sxs-lookup"><span data-stu-id="da38c-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="da38c-150">从**名称**列中选择*服务器 API 应用*（例如，**Blazor服务器 AAD B2C**）。</span><span class="sxs-lookup"><span data-stu-id="da38c-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="da38c-151">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="da38c-151">Open the **API** list.</span></span>
1. <span data-ttu-id="da38c-152">启用对 API 的访问（例如， `API.Access`</span><span class="sxs-lookup"><span data-stu-id="da38c-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="da38c-153">选择“添加权限”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="da38c-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="da38c-154">选择 **[TENANT 名称] 按钮的授予管理员内容**。</span><span class="sxs-lookup"><span data-stu-id="da38c-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="da38c-155">请选择“是”以确认。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="da38c-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="da38c-156">在**家庭** > **Azure AD B2C** > **用户流中**：</span><span class="sxs-lookup"><span data-stu-id="da38c-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="da38c-157">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="da38c-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="da38c-158">至少，选择**应用程序声明** > **显示名称**用户属性以填充`context.User.Identity.Name``LoginDisplay`组件中的 （*共享/LoginDisplay.razor）。*</span><span class="sxs-lookup"><span data-stu-id="da38c-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="da38c-159">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="da38c-159">Record the following information:</span></span>

* <span data-ttu-id="da38c-160">记录*客户端应用*应用程序 ID（客户端 ID）（例如。 `33333333-3333-3333-3333-333333333333`</span><span class="sxs-lookup"><span data-stu-id="da38c-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="da38c-161">记录为应用创建的注册和登录用户流名称（例如。 `B2C_1_signupsignin`</span><span class="sxs-lookup"><span data-stu-id="da38c-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="da38c-162">创建应用</span><span class="sxs-lookup"><span data-stu-id="da38c-162">Create the app</span></span>

<span data-ttu-id="da38c-163">将以下命令中的占位符替换为前面记录的信息，并在命令 shell 中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="da38c-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="da38c-164">要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample`</span><span class="sxs-lookup"><span data-stu-id="da38c-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="da38c-165">文件夹名称也将成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="da38c-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="da38c-166">将应用 ID URI`app-id-uri`传递给该选项，但请注意客户端应用中可能需要更改配置，这在[Access 令牌作用域](#access-token-scopes)部分中对此进行了说明。</span><span class="sxs-lookup"><span data-stu-id="da38c-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="da38c-167">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="da38c-167">Server app configuration</span></span>

<span data-ttu-id="da38c-168">*本节涉及解决方案的 **"服务器"** 应用。*</span><span class="sxs-lookup"><span data-stu-id="da38c-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="da38c-169">身份验证包</span><span class="sxs-lookup"><span data-stu-id="da38c-169">Authentication package</span></span>

<span data-ttu-id="da38c-170">对验证和授权调用ASP.NET核心 Web API 的支持由： `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`</span><span class="sxs-lookup"><span data-stu-id="da38c-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="da38c-171">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="da38c-171">Authentication service support</span></span>

<span data-ttu-id="da38c-172">该方法`AddAuthentication`在应用中设置身份验证服务，并将 JWT 承载处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="da38c-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="da38c-173">该方法`AddAzureADB2CBearer`在 JWT 承载处理程序中设置验证 Azure 活动目录 B2C 发出的令牌所需的特定参数：</span><span class="sxs-lookup"><span data-stu-id="da38c-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="da38c-174">`UseAuthentication`并确保`UseAuthorization`：</span><span class="sxs-lookup"><span data-stu-id="da38c-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="da38c-175">应用尝试对传入请求解析和验证令牌。</span><span class="sxs-lookup"><span data-stu-id="da38c-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="da38c-176">尝试在没有适当凭据的情况下访问受保护资源的任何请求都失败。</span><span class="sxs-lookup"><span data-stu-id="da38c-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="da38c-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="da38c-177">User.Identity.Name</span></span>

<span data-ttu-id="da38c-178">默认情况下，未填充`User.Identity.Name`。</span><span class="sxs-lookup"><span data-stu-id="da38c-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="da38c-179">要将应用配置为从`name`声明类型接收值，请配置<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>中的`Startup.ConfigureServices`[令牌验证参数。](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)</span><span class="sxs-lookup"><span data-stu-id="da38c-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="da38c-180">应用设置</span><span class="sxs-lookup"><span data-stu-id="da38c-180">App settings</span></span>

<span data-ttu-id="da38c-181">*appsettings.json*文件包含用于配置用于验证访问令牌的 JWT 承载处理程序的选项。</span><span class="sxs-lookup"><span data-stu-id="da38c-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="da38c-182">天气预报控制器</span><span class="sxs-lookup"><span data-stu-id="da38c-182">WeatherForecast controller</span></span>

<span data-ttu-id="da38c-183">天气预报控制器 （*控制器/天气预报控制器.cs*） 公开受保护的 API，`[Authorize]`该属性应用于控制器。</span><span class="sxs-lookup"><span data-stu-id="da38c-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="da38c-184">**请务必**了解：</span><span class="sxs-lookup"><span data-stu-id="da38c-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="da38c-185">此`[Authorize]`API 控制器中的属性是保护此 API 免受未经授权的访问的唯一原因。</span><span class="sxs-lookup"><span data-stu-id="da38c-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="da38c-186">Blazor WebAssembly 应用中使用的`[Authorize]`属性仅用作应用的提示，即应授权用户使应用正常工作。</span><span class="sxs-lookup"><span data-stu-id="da38c-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="da38c-187">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="da38c-187">Client app configuration</span></span>

<span data-ttu-id="da38c-188">*本节涉及解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="da38c-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="da38c-189">身份验证包</span><span class="sxs-lookup"><span data-stu-id="da38c-189">Authentication package</span></span>

<span data-ttu-id="da38c-190">当创建应用以使用单个 B2C 帐户 （）`IndividualB2C`时，应用会自动接收 Microsoft[身份验证库](/azure/active-directory/develop/msal-overview)（）`Microsoft.Authentication.WebAssembly.Msal`的包引用 。</span><span class="sxs-lookup"><span data-stu-id="da38c-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="da38c-191">该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="da38c-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="da38c-192">如果向应用添加身份验证，则手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="da38c-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="da38c-193">在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。</span><span class="sxs-lookup"><span data-stu-id="da38c-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="da38c-194">包`Microsoft.Authentication.WebAssembly.Msal`会临时将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="da38c-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="da38c-195">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="da38c-195">Authentication service support</span></span>

<span data-ttu-id="da38c-196">使用`AddMsalAuthentication``Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="da38c-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="da38c-197">此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="da38c-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="da38c-198">*Program.cs*：</span><span class="sxs-lookup"><span data-stu-id="da38c-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="da38c-199">该方法`AddMsalAuthentication`接受回调以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="da38c-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="da38c-200">注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="da38c-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="da38c-201">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="da38c-201">Access token scopes</span></span>

<span data-ttu-id="da38c-202">默认访问令牌作用域表示访问令牌作用域的列表，这些作用域是：</span><span class="sxs-lookup"><span data-stu-id="da38c-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="da38c-203">默认情况下包含在登录请求中。</span><span class="sxs-lookup"><span data-stu-id="da38c-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="da38c-204">用于在身份验证后立即预配访问令牌。</span><span class="sxs-lookup"><span data-stu-id="da38c-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="da38c-205">根据 Azure 活动目录规则，所有作用域必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="da38c-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="da38c-206">根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="da38c-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="da38c-207">如果 Azure 门户提供作用域 URI，并且应用在收到来自 API 的*401 未授权*响应时**引发未处理的异常**，请尝试使用不包括方案和主机的范围 URI。</span><span class="sxs-lookup"><span data-stu-id="da38c-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="da38c-208">例如，Azure 门户可以提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="da38c-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="da38c-209">提供无方案和主机的范围 URI：</span><span class="sxs-lookup"><span data-stu-id="da38c-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="da38c-210">有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。</span><span class="sxs-lookup"><span data-stu-id="da38c-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="da38c-211">导入文件</span><span class="sxs-lookup"><span data-stu-id="da38c-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="da38c-212">索引页面</span><span class="sxs-lookup"><span data-stu-id="da38c-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="da38c-213">应用组件</span><span class="sxs-lookup"><span data-stu-id="da38c-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="da38c-214">重定向到登录组件</span><span class="sxs-lookup"><span data-stu-id="da38c-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="da38c-215">登录显示组件</span><span class="sxs-lookup"><span data-stu-id="da38c-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="da38c-216">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="da38c-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="da38c-217">提取数据组件</span><span class="sxs-lookup"><span data-stu-id="da38c-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="da38c-218">运行应用</span><span class="sxs-lookup"><span data-stu-id="da38c-218">Run the app</span></span>

<span data-ttu-id="da38c-219">从"服务器"项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="da38c-219">Run the app from the Server project.</span></span> <span data-ttu-id="da38c-220">使用 Visual Studio 时，在**解决方案资源管理器**中选择"服务器"项目，然后选择工具栏中的 **"运行"** 按钮，或从 **"调试"** 菜单启动应用。</span><span class="sxs-lookup"><span data-stu-id="da38c-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="da38c-221">其他资源</span><span class="sxs-lookup"><span data-stu-id="da38c-221">Additional resources</span></span>

* [<span data-ttu-id="da38c-222">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="da38c-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="da38c-223">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="da38c-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="da38c-224">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="da38c-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
