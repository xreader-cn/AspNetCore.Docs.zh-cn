---
title: 使用 Azure Active Directory 保护Blazor ASP.NET Core WebAssembly 托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 3a2f3bdd194b9153c5d59af7adfad3a3c8c56b23
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776033"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="29799-102">使用 Azure Active Directory 保护Blazor ASP.NET Core WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="29799-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="29799-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29799-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="29799-104">本文介绍如何创建使用[Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的[ Blazor WebAssembly 托管应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="29799-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="29799-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="29799-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="29799-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="29799-106">Create a tenant</span></span>

<span data-ttu-id="29799-107">按照[快速入门：设置租户](/azure/active-directory/develop/quickstart-create-new-tenant)中的指导在 AAD 中创建租户。</span><span class="sxs-lookup"><span data-stu-id="29799-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="29799-108">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="29799-108">Register a server API app</span></span>

<span data-ttu-id="29799-109">按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**"区域中为*服务器 API 应用*注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="29799-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="29799-110">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="29799-110">Select **New registration**.</span></span>
1. <span data-ttu-id="29799-111">提供应用的**名称**（例如， \*\* Blazor服务器 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="29799-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="29799-112">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="29799-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="29799-113">对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。</span><span class="sxs-lookup"><span data-stu-id="29799-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="29799-114">在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。</span><span class="sxs-lookup"><span data-stu-id="29799-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="29799-115">禁用 "**将** > **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="29799-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="29799-116">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="29799-116">Select **Register**.</span></span>

<span data-ttu-id="29799-117">在 " **API 权限**" 中，删除**Microsoft Graph** > **用户. 读取**权限，因为应用程序不需要登录或 uer 配置文件访问。</span><span class="sxs-lookup"><span data-stu-id="29799-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="29799-118">在中**公开 API**：</span><span class="sxs-lookup"><span data-stu-id="29799-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="29799-119">选择“添加范围”。 </span><span class="sxs-lookup"><span data-stu-id="29799-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="29799-120">选择“保存并继续”。 </span><span class="sxs-lookup"><span data-stu-id="29799-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="29799-121">提供**作用域名称**（例如`API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="29799-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="29799-122">提供**管理员同意显示名称**（例如`Access API`）。</span><span class="sxs-lookup"><span data-stu-id="29799-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="29799-123">提供**管理员同意说明**（例如`Allows the app to access server app API endpoints.`）。</span><span class="sxs-lookup"><span data-stu-id="29799-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="29799-124">确认 "**状态**" 设置为 "**已启用**"。</span><span class="sxs-lookup"><span data-stu-id="29799-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="29799-125">选择“添加作用域”。 </span><span class="sxs-lookup"><span data-stu-id="29799-125">Select **Add scope**.</span></span>

<span data-ttu-id="29799-126">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="29799-126">Record the following information:</span></span>

* <span data-ttu-id="29799-127">*服务器 API 应用*应用程序 ID （客户端 ID）（例如`11111111-1111-1111-1111-111111111111`，）</span><span class="sxs-lookup"><span data-stu-id="29799-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="29799-128">应用 ID URI （例如`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`，、或提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="29799-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="29799-129">目录 ID （租户 ID）（例如， `222222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="29799-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="29799-130">AAD 租户域（例如`contoso.onmicrosoft.com`）</span><span class="sxs-lookup"><span data-stu-id="29799-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="29799-131">默认作用域（例如`API.Access`）</span><span class="sxs-lookup"><span data-stu-id="29799-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="29799-132">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="29799-132">Register a client app</span></span>

<span data-ttu-id="29799-133">按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**"区域中为*客户端应用程序*注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="29799-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="29799-134">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="29799-134">Select **New registration**.</span></span>
1. <span data-ttu-id="29799-135">提供应用的**名称**（例如， \*\* Blazor客户端 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="29799-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="29799-136">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="29799-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="29799-137">对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。</span><span class="sxs-lookup"><span data-stu-id="29799-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="29799-138">将 "**重定向 uri** " 下拉状态设置为 " **Web**"，并提供`https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="29799-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="29799-139">禁用 "**将** > **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="29799-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="29799-140">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="29799-140">Select **Register**.</span></span>

<span data-ttu-id="29799-141">在 "**身份验证** > **平台配置** > "**Web**：</span><span class="sxs-lookup"><span data-stu-id="29799-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="29799-142">确认存在的**重定向 URI** `https://localhost:5001/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="29799-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="29799-143">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="29799-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="29799-144">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="29799-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="29799-145">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="29799-145">Select the **Save** button.</span></span>

<span data-ttu-id="29799-146">在 " **API 权限**：</span><span class="sxs-lookup"><span data-stu-id="29799-146">In **API permissions**:</span></span>

1. <span data-ttu-id="29799-147">确认应用程序具有**Microsoft Graph** > 的 "**用户**" 权限。</span><span class="sxs-lookup"><span data-stu-id="29799-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="29799-148">选择 "**添加权限**"，然后选择 **"我的 api"**。</span><span class="sxs-lookup"><span data-stu-id="29799-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="29799-149">从 "**名称**" 列中选择*服务器 API 应用*（例如， \*\* Blazor服务器 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="29799-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="29799-150">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="29799-150">Open the **API** list.</span></span>
1. <span data-ttu-id="29799-151">启用对 API 的访问（例如`API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="29799-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="29799-152">选择“添加权限”  。</span><span class="sxs-lookup"><span data-stu-id="29799-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="29799-153">选择 "**为 {租户名称} 授予管理内容**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="29799-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="29799-154">单击“是”  以确认。</span><span class="sxs-lookup"><span data-stu-id="29799-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="29799-155">记录*客户端应用*应用程序 Id （客户端 id）（例如`33333333-3333-3333-3333-333333333333`）。</span><span class="sxs-lookup"><span data-stu-id="29799-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="29799-156">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="29799-156">Create the app</span></span>

<span data-ttu-id="29799-157">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="29799-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="29799-158">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。</span><span class="sxs-lookup"><span data-stu-id="29799-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="29799-159">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="29799-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="29799-160">将应用 ID URI 传递给`app-id-uri`选项，但请注意，可能需要在客户端应用中进行配置更改，这在 "[访问令牌范围](#access-token-scopes)" 部分中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="29799-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="29799-161">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="29799-161">Server app configuration</span></span>

<span data-ttu-id="29799-162">*本部分适用于解决方案的**服务器**应用。*</span><span class="sxs-lookup"><span data-stu-id="29799-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="29799-163">身份验证包</span><span class="sxs-lookup"><span data-stu-id="29799-163">Authentication package</span></span>

<span data-ttu-id="29799-164">提供对 ASP.NET Core Web Api 的身份验证和授权的支持是由提供`Microsoft.AspNetCore.Authentication.AzureAD.UI`的：</span><span class="sxs-lookup"><span data-stu-id="29799-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="29799-165">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="29799-165">Authentication service support</span></span>

<span data-ttu-id="29799-166">`AddAuthentication`方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认的身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="29799-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="29799-167">`AddAzureADBearer`方法在验证 Azure Active Directory 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：</span><span class="sxs-lookup"><span data-stu-id="29799-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="29799-168">`UseAuthentication`并`UseAuthorization`确保：</span><span class="sxs-lookup"><span data-stu-id="29799-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="29799-169">应用尝试分析和验证传入请求的令牌。</span><span class="sxs-lookup"><span data-stu-id="29799-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="29799-170">任何试图访问受保护资源的请求均不正确。</span><span class="sxs-lookup"><span data-stu-id="29799-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="29799-171">用户.Identity.路径名</span><span class="sxs-lookup"><span data-stu-id="29799-171">User.Identity.Name</span></span>

<span data-ttu-id="29799-172">默认情况下，服务器应用 API 使用`User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`声明类型中的值（例如`2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`）进行填充。</span><span class="sxs-lookup"><span data-stu-id="29799-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="29799-173">若要将应用配置为接收来自`name`声明类型的值，请<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>在中`Startup.ConfigureServices`配置[TokenValidationParameters](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ：</span><span class="sxs-lookup"><span data-stu-id="29799-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="29799-174">应用设置</span><span class="sxs-lookup"><span data-stu-id="29799-174">App settings</span></span>

<span data-ttu-id="29799-175">*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项。</span><span class="sxs-lookup"><span data-stu-id="29799-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="29799-176">示例：</span><span class="sxs-lookup"><span data-stu-id="29799-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="29799-177">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="29799-177">WeatherForecast controller</span></span>

<span data-ttu-id="29799-178">WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，该`[Authorize]` API 的属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="29799-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="29799-179">**务必**要了解：</span><span class="sxs-lookup"><span data-stu-id="29799-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="29799-180">此`[Authorize]` api 控制器中的属性只是保护此 api 不受未经授权的访问。</span><span class="sxs-lookup"><span data-stu-id="29799-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="29799-181">WebAssembly 应用程序中使用的`[Authorize]`属性仅作为对应用程序的提示，用户应授权该应用程序正常工作。 Blazor</span><span class="sxs-lookup"><span data-stu-id="29799-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="29799-182">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="29799-182">Client app configuration</span></span>

<span data-ttu-id="29799-183">*本部分适用于解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="29799-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="29799-184">身份验证包</span><span class="sxs-lookup"><span data-stu-id="29799-184">Authentication package</span></span>

<span data-ttu-id="29799-185">创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="29799-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="29799-186">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="29799-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="29799-187">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="29799-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="29799-188">将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。</span><span class="sxs-lookup"><span data-stu-id="29799-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="29799-189">`Microsoft.Authentication.WebAssembly.Msal`包可传递将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="29799-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="29799-190">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="29799-190">Authentication service support</span></span>

<span data-ttu-id="29799-191">添加了`HttpClient`对实例的支持，其中包括对服务器项目发出请求时的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="29799-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="29799-192">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="29799-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="29799-193">使用`AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="29799-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="29799-194">此方法设置应用与Identity提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="29799-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="29799-195">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="29799-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="29799-196">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="29799-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="29799-197">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="29799-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="29799-198">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="29799-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="29799-199">示例：</span><span class="sxs-lookup"><span data-stu-id="29799-199">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="29799-200">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="29799-200">Access token scopes</span></span>

<span data-ttu-id="29799-201">默认访问令牌范围表示访问令牌作用域的列表：</span><span class="sxs-lookup"><span data-stu-id="29799-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="29799-202">默认情况下，在登录请求中包括。</span><span class="sxs-lookup"><span data-stu-id="29799-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="29799-203">用于在身份验证后立即设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="29799-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="29799-204">对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="29799-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="29799-205">可以根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="29799-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="29799-206">如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="29799-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="29799-207">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="29799-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="29799-208">提供不含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="29799-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="29799-209">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="29799-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="29799-210">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="29799-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="29799-211">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="29799-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="29799-212">导入文件</span><span class="sxs-lookup"><span data-stu-id="29799-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="29799-213">索引页面</span><span class="sxs-lookup"><span data-stu-id="29799-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="29799-214">应用组件</span><span class="sxs-lookup"><span data-stu-id="29799-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="29799-215">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="29799-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="29799-216">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="29799-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="29799-217">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="29799-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="29799-218">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="29799-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="29799-219">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="29799-219">Run the app</span></span>

<span data-ttu-id="29799-220">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="29799-220">Run the app from the Server project.</span></span> <span data-ttu-id="29799-221">使用 Visual Studio 时，请在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="29799-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="29799-222">其他资源</span><span class="sxs-lookup"><span data-stu-id="29799-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="29799-223">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="29799-223">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
