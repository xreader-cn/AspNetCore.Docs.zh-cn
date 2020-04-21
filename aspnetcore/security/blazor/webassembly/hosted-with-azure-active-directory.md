---
title: 使用 AzureBlazor活动目录保护ASP.NET核心 Web 组件托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661835"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="efc69-102">使用 AzureBlazor活动目录保护ASP.NET核心 Web 组件托管应用</span><span class="sxs-lookup"><span data-stu-id="efc69-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="efc69-103">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="efc69-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="efc69-104">本文介绍如何创建使用[Azure 活动目录 （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的[BlazorWebAssembly 托管应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="efc69-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="efc69-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="efc69-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="efc69-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="efc69-106">Create a tenant</span></span>

<span data-ttu-id="efc69-107">按照["快速入门"中的指南：设置租户](/azure/active-directory/develop/quickstart-create-new-tenant)以在 AAD 中创建租户。</span><span class="sxs-lookup"><span data-stu-id="efc69-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="efc69-108">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="efc69-108">Register a server API app</span></span>

<span data-ttu-id="efc69-109">按照["快速入门"中的指南操作：将应用程序注册到 Microsoft 标识平台](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题，以在 Azure 门户的**Azure 活动目录** > **应用注册**区域注册*服务器 API 应用*的 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="efc69-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="efc69-110">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="efc69-110">Select **New registration**.</span></span>
1. <span data-ttu-id="efc69-111">为应用提供**名称**（例如，**Blazor服务器 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="efc69-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="efc69-112">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="efc69-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="efc69-113">对于此体验，您只能选择**此组织目录中的帐户**（单个租户）。</span><span class="sxs-lookup"><span data-stu-id="efc69-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="efc69-114">在这种情况下，*服务器 API 应用*不需要重定向**URI，** 因此请将下拉列表设置为**Web，** 并且不输入重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="efc69-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="efc69-115">禁用 **"权限** > **授予管理员集中打开"和offline_access权限**复选框。</span><span class="sxs-lookup"><span data-stu-id="efc69-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="efc69-116">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="efc69-116">Select **Register**.</span></span>

<span data-ttu-id="efc69-117">在**API 权限**中，删除 Microsoft**图形** > **用户.读取**权限，因为应用不需要登录或访问配置文件。</span><span class="sxs-lookup"><span data-stu-id="efc69-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="efc69-118">在**公开 API 中**：</span><span class="sxs-lookup"><span data-stu-id="efc69-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="efc69-119">选择 **"添加范围**"。</span><span class="sxs-lookup"><span data-stu-id="efc69-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="efc69-120">选择“保存并继续”。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="efc69-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="efc69-121">提供**范围名称**（例如， `API.Access`</span><span class="sxs-lookup"><span data-stu-id="efc69-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="efc69-122">提供**管理员同意显示名称**（例如， `Access API`</span><span class="sxs-lookup"><span data-stu-id="efc69-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="efc69-123">提供**管理员同意说明**（例如， `Allows the app to access server app API endpoints.`</span><span class="sxs-lookup"><span data-stu-id="efc69-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="efc69-124">确认**状态**设置为**启用**。</span><span class="sxs-lookup"><span data-stu-id="efc69-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="efc69-125">选择 **"添加范围**"。</span><span class="sxs-lookup"><span data-stu-id="efc69-125">Select **Add scope**.</span></span>

<span data-ttu-id="efc69-126">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="efc69-126">Record the following information:</span></span>

* <span data-ttu-id="efc69-127">*服务器 API 应用*应用程序 ID（客户端 ID）（例如`11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="efc69-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="efc69-128">应用 ID URI（例如`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`，`api://11111111-1111-1111-1111-111111111111`或您提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="efc69-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="efc69-129">目录 ID（租户 ID）（例如`222222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="efc69-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="efc69-130">AAD 租户域（例如） `contoso.onmicrosoft.com`</span><span class="sxs-lookup"><span data-stu-id="efc69-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="efc69-131">默认作用域（例如）， `API.Access`</span><span class="sxs-lookup"><span data-stu-id="efc69-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="efc69-132">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="efc69-132">Register a client app</span></span>

<span data-ttu-id="efc69-133">按照["快速入门"中的指南操作：将应用程序注册到 Microsoft 标识平台](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题，以在 Azure 门户的**Azure 活动目录** > **应用注册**区域注册*客户端应用*的 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="efc69-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="efc69-134">选择“新注册”。 </span><span class="sxs-lookup"><span data-stu-id="efc69-134">Select **New registration**.</span></span>
1. <span data-ttu-id="efc69-135">为应用提供**名称**（例如，**Blazor客户端 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="efc69-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="efc69-136">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="efc69-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="efc69-137">对于此体验，您只能选择**此组织目录中的帐户**（单个租户）。</span><span class="sxs-lookup"><span data-stu-id="efc69-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="efc69-138">将**重定向 URI**下拉列表设置为**Web，** 并提供 重定向 URI。 `https://localhost:5001/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="efc69-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="efc69-139">禁用 **"权限** > **授予管理员集中打开"和offline_access权限**复选框。</span><span class="sxs-lookup"><span data-stu-id="efc69-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="efc69-140">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="efc69-140">Select **Register**.</span></span>

<span data-ttu-id="efc69-141">在**身份验证** > **平台配置中** > **，Web**：</span><span class="sxs-lookup"><span data-stu-id="efc69-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="efc69-142">确认存在 重定向`https://localhost:5001/authentication/login-callback` **URI。**</span><span class="sxs-lookup"><span data-stu-id="efc69-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="efc69-143">对于**隐式授予**，选择 Access**令牌**和**ID 令牌的**复选框。</span><span class="sxs-lookup"><span data-stu-id="efc69-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="efc69-144">此体验可以接受应用的剩余默认值。</span><span class="sxs-lookup"><span data-stu-id="efc69-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="efc69-145">选择"**保存**"按钮。</span><span class="sxs-lookup"><span data-stu-id="efc69-145">Select the **Save** button.</span></span>

<span data-ttu-id="efc69-146">在**API 权限**中：</span><span class="sxs-lookup"><span data-stu-id="efc69-146">In **API permissions**:</span></span>

1. <span data-ttu-id="efc69-147">确认应用具有**Microsoft 图形** > **用户.读取**权限。</span><span class="sxs-lookup"><span data-stu-id="efc69-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="efc69-148">选择 **"添加"** 后跟**我的 API 的权限**。</span><span class="sxs-lookup"><span data-stu-id="efc69-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="efc69-149">从 **"名称"** 列中选择*服务器 API 应用*（例如，**Blazor服务器 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="efc69-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="efc69-150">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="efc69-150">Open the **API** list.</span></span>
1. <span data-ttu-id="efc69-151">启用对 API 的访问（例如， `API.Access`</span><span class="sxs-lookup"><span data-stu-id="efc69-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="efc69-152">选择“添加权限”  。</span><span class="sxs-lookup"><span data-stu-id="efc69-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="efc69-153">选择 **[TENANT 名称] 按钮的授予管理员内容**。</span><span class="sxs-lookup"><span data-stu-id="efc69-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="efc69-154">请选择“是”以确认。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="efc69-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="efc69-155">记录*客户端应用*应用程序 ID（客户端 ID）（例如。 `33333333-3333-3333-3333-333333333333`</span><span class="sxs-lookup"><span data-stu-id="efc69-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="efc69-156">创建应用</span><span class="sxs-lookup"><span data-stu-id="efc69-156">Create the app</span></span>

<span data-ttu-id="efc69-157">将以下命令中的占位符替换为前面记录的信息，并在命令 shell 中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="efc69-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="efc69-158">要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample`</span><span class="sxs-lookup"><span data-stu-id="efc69-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="efc69-159">文件夹名称也将成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="efc69-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="efc69-160">将应用 ID URI`app-id-uri`传递给该选项，但请注意客户端应用中可能需要更改配置，这在[Access 令牌作用域](#access-token-scopes)部分中对此进行了说明。</span><span class="sxs-lookup"><span data-stu-id="efc69-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="efc69-161">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="efc69-161">Server app configuration</span></span>

<span data-ttu-id="efc69-162">*本节涉及解决方案的 **"服务器"** 应用。*</span><span class="sxs-lookup"><span data-stu-id="efc69-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="efc69-163">身份验证包</span><span class="sxs-lookup"><span data-stu-id="efc69-163">Authentication package</span></span>

<span data-ttu-id="efc69-164">对验证和授权调用ASP.NET核心 Web API 的支持由： `Microsoft.AspNetCore.Authentication.AzureAD.UI`</span><span class="sxs-lookup"><span data-stu-id="efc69-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="efc69-165">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="efc69-165">Authentication service support</span></span>

<span data-ttu-id="efc69-166">该方法`AddAuthentication`在应用中设置身份验证服务，并将 JWT 承载处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="efc69-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="efc69-167">该方法`AddAzureADBearer`在 JWT 承载处理程序中设置验证 Azure 活动目录发出的令牌所需的特定参数：</span><span class="sxs-lookup"><span data-stu-id="efc69-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="efc69-168">`UseAuthentication`并确保`UseAuthorization`：</span><span class="sxs-lookup"><span data-stu-id="efc69-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="efc69-169">应用尝试对传入请求解析和验证令牌。</span><span class="sxs-lookup"><span data-stu-id="efc69-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="efc69-170">尝试在没有适当凭据的情况下访问受保护资源的任何请求都失败。</span><span class="sxs-lookup"><span data-stu-id="efc69-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="efc69-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="efc69-171">User.Identity.Name</span></span>

<span data-ttu-id="efc69-172">默认情况下，服务器应用 API`User.Identity.Name`使用`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`声明类型中的值填充（例如， `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`。</span><span class="sxs-lookup"><span data-stu-id="efc69-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="efc69-173">要将应用配置为从`name`声明类型接收值，请配置<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>中的`Startup.ConfigureServices`[令牌验证参数。](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)</span><span class="sxs-lookup"><span data-stu-id="efc69-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="efc69-174">应用设置</span><span class="sxs-lookup"><span data-stu-id="efc69-174">App settings</span></span>

<span data-ttu-id="efc69-175">*appsettings.json*文件包含用于配置用于验证访问令牌的 JWT 承载处理程序的选项。</span><span class="sxs-lookup"><span data-stu-id="efc69-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="efc69-176">天气预报控制器</span><span class="sxs-lookup"><span data-stu-id="efc69-176">WeatherForecast controller</span></span>

<span data-ttu-id="efc69-177">天气预报控制器 （*控制器/天气预报控制器.cs*） 公开受保护的 API，`[Authorize]`该属性应用于控制器。</span><span class="sxs-lookup"><span data-stu-id="efc69-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="efc69-178">**请务必**了解：</span><span class="sxs-lookup"><span data-stu-id="efc69-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="efc69-179">此`[Authorize]`API 控制器中的属性是保护此 API 免受未经授权的访问的唯一原因。</span><span class="sxs-lookup"><span data-stu-id="efc69-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="efc69-180">Blazor WebAssembly 应用中使用的`[Authorize]`属性仅用作应用的提示，即应授权用户使应用正常工作。</span><span class="sxs-lookup"><span data-stu-id="efc69-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="efc69-181">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="efc69-181">Client app configuration</span></span>

<span data-ttu-id="efc69-182">*本节涉及解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="efc69-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="efc69-183">身份验证包</span><span class="sxs-lookup"><span data-stu-id="efc69-183">Authentication package</span></span>

<span data-ttu-id="efc69-184">当创建应用以使用工作帐户或学校帐户 （）`SingleOrg`时，应用会自动接收 Microsoft[身份验证库](/azure/active-directory/develop/msal-overview)（）`Microsoft.Authentication.WebAssembly.Msal`的包引用 。</span><span class="sxs-lookup"><span data-stu-id="efc69-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="efc69-185">该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="efc69-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="efc69-186">如果向应用添加身份验证，则手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="efc69-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="efc69-187">在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。</span><span class="sxs-lookup"><span data-stu-id="efc69-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="efc69-188">包`Microsoft.Authentication.WebAssembly.Msal`会临时将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="efc69-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="efc69-189">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="efc69-189">Authentication service support</span></span>

<span data-ttu-id="efc69-190">使用`AddMsalAuthentication``Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="efc69-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="efc69-191">此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="efc69-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="efc69-192">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="efc69-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="efc69-193">该方法`AddMsalAuthentication`接受回调以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="efc69-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="efc69-194">注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="efc69-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="efc69-195">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="efc69-195">Access token scopes</span></span>

<span data-ttu-id="efc69-196">默认访问令牌作用域表示访问令牌作用域的列表，这些作用域是：</span><span class="sxs-lookup"><span data-stu-id="efc69-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="efc69-197">默认情况下包含在登录请求中。</span><span class="sxs-lookup"><span data-stu-id="efc69-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="efc69-198">用于在身份验证后立即预配访问令牌。</span><span class="sxs-lookup"><span data-stu-id="efc69-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="efc69-199">根据 Azure 活动目录规则，所有作用域必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="efc69-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="efc69-200">根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="efc69-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="efc69-201">如果 Azure 门户提供作用域 URI，并且应用在收到来自 API 的*401 未授权*响应时**引发未处理的异常**，请尝试使用不包括方案和主机的范围 URI。</span><span class="sxs-lookup"><span data-stu-id="efc69-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="efc69-202">例如，Azure 门户可以提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="efc69-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="efc69-203">提供无方案和主机的范围 URI：</span><span class="sxs-lookup"><span data-stu-id="efc69-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="efc69-204">有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。</span><span class="sxs-lookup"><span data-stu-id="efc69-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="efc69-205">导入文件</span><span class="sxs-lookup"><span data-stu-id="efc69-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="efc69-206">索引页面</span><span class="sxs-lookup"><span data-stu-id="efc69-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="efc69-207">应用组件</span><span class="sxs-lookup"><span data-stu-id="efc69-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="efc69-208">重定向到登录组件</span><span class="sxs-lookup"><span data-stu-id="efc69-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="efc69-209">登录显示组件</span><span class="sxs-lookup"><span data-stu-id="efc69-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="efc69-210">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="efc69-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="efc69-211">提取数据组件</span><span class="sxs-lookup"><span data-stu-id="efc69-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="efc69-212">运行应用</span><span class="sxs-lookup"><span data-stu-id="efc69-212">Run the app</span></span>

<span data-ttu-id="efc69-213">从"服务器"项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="efc69-213">Run the app from the Server project.</span></span> <span data-ttu-id="efc69-214">使用 Visual Studio 时，在**解决方案资源管理器**中选择"服务器"项目，然后选择工具栏中的 **"运行"** 按钮，或从 **"调试"** 菜单启动应用。</span><span class="sxs-lookup"><span data-stu-id="efc69-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="efc69-215">其他资源</span><span class="sxs-lookup"><span data-stu-id="efc69-215">Additional resources</span></span>

* [<span data-ttu-id="efc69-216">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="efc69-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="efc69-217">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="efc69-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
