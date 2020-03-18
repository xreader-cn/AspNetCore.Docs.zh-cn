---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 2ddbc9791ec9b31d55c9c6017d9d6d5be5c8dec8
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434481"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="dcbbe-102">使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="dcbbe-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="dcbbe-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dcbbe-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="dcbbe-104">本文介绍如何创建使用[Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的[Blazor WebAssembly 托管应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="dcbbe-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="dcbbe-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="dcbbe-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="dcbbe-106">Create a tenant</span></span>

<span data-ttu-id="dcbbe-107">按照[快速入门：设置租户](/azure/active-directory/develop/quickstart-create-new-tenant)中的指导在 AAD 中创建租户。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="dcbbe-108">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="dcbbe-108">Register a server API app</span></span>

<span data-ttu-id="dcbbe-109">请按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，在 Azure 门户的 " **Azure Active Directory** > **应用注册**" 区域中为*服务器 API 应用*注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="dcbbe-110">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-110">Select **New registration**.</span></span>
1. <span data-ttu-id="dcbbe-111">提供应用的**名称**（例如 **Blazor 服务器 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="dcbbe-112">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="dcbbe-113">对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="dcbbe-114">在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="dcbbe-115">禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="dcbbe-116">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-116">Select **Register**.</span></span>

<span data-ttu-id="dcbbe-117">在 " **API 权限**" 中，删除**Microsoft Graph** > **用户 "。读取**权限，因为应用不需要登录或 uer 配置文件访问。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="dcbbe-118">在中**公开 API**：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="dcbbe-119">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="dcbbe-120">选择“保存并继续”。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="dcbbe-121">提供**作用域名称**（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="dcbbe-122">提供**管理员同意显示名称**（例如 `Access API`）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="dcbbe-123">提供**管理员同意说明**（例如 `Allows the app to access server app API endpoints.`）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="dcbbe-124">确认 "**状态**" 设置为 "**已启用**"。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="dcbbe-125">选择 "**添加作用域**"。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-125">Select **Add scope**.</span></span>

<span data-ttu-id="dcbbe-126">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-126">Record the following information:</span></span>

* <span data-ttu-id="dcbbe-127">*服务器 API 应用*应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="dcbbe-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="dcbbe-128">目录 ID （租户 ID）（例如 `222222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="dcbbe-128">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="dcbbe-129">AAD 租户域（例如 `contoso.onmicrosoft.com`）</span><span class="sxs-lookup"><span data-stu-id="dcbbe-129">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="dcbbe-130">默认作用域（例如 `API.Access`）</span><span class="sxs-lookup"><span data-stu-id="dcbbe-130">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="dcbbe-131">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="dcbbe-131">Register a client app</span></span>

<span data-ttu-id="dcbbe-132">请按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**区域中为*客户端应用程序*注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-132">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="dcbbe-133">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-133">Select **New registration**.</span></span>
1. <span data-ttu-id="dcbbe-134">提供应用的**名称**（例如， **Blazor 客户端 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-134">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="dcbbe-135">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-135">Choose a **Supported account types**.</span></span> <span data-ttu-id="dcbbe-136">对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-136">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="dcbbe-137">将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-137">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="dcbbe-138">禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-138">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="dcbbe-139">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-139">Select **Register**.</span></span>

<span data-ttu-id="dcbbe-140">在 "**身份验证**" > **平台配置** > **Web**：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-140">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="dcbbe-141">确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-141">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="dcbbe-142">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-142">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="dcbbe-143">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="dcbbe-144">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-144">Select the **Save** button.</span></span>

<span data-ttu-id="dcbbe-145">在 " **API 权限**：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-145">In **API permissions**:</span></span>

1. <span data-ttu-id="dcbbe-146">确认应用程序已**Microsoft Graph** > **用户。读取**权限。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-146">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="dcbbe-147">选择 "**添加权限**"，然后选择 **"我的 api"** 。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-147">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="dcbbe-148">从 "**名称**" 列中选择 "*服务器 API 应用*" （例如 **Blazor 服务器 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-148">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="dcbbe-149">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-149">Open the **API** list.</span></span>
1. <span data-ttu-id="dcbbe-150">启用对 API 的访问（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-150">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="dcbbe-151">选择“添加权限”。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-151">Select **Add permissions**.</span></span>
1. <span data-ttu-id="dcbbe-152">选择 "**为 {租户名称} 授予管理内容**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-152">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="dcbbe-153">请选择“是”以确认。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-153">Select **Yes** to confirm.</span></span>

<span data-ttu-id="dcbbe-154">记录*客户端应用*应用程序 Id （客户端 id）（例如 `33333333-3333-3333-3333-333333333333`）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-154">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="dcbbe-155">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="dcbbe-155">Create the app</span></span>

<span data-ttu-id="dcbbe-156">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-156">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="dcbbe-157">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-157">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="dcbbe-158">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-158">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="dcbbe-159">请参阅[身份验证服务支持](#Authentication service support)部分，了解对默认访问令牌范围的重要配置更改。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-159">See the [Authentication service support](#Authentication service support) section for an important configuration change to the default access token scope.</span></span> <span data-ttu-id="dcbbe-160">从模板创建*客户端应用*后，必须手动更改 Blazor WebAssembly 模板提供的值。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-160">The value provided by the Blazor WebAssembly template must be manually changed after the *Client app* is created from the template.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="dcbbe-161">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="dcbbe-161">Server app configuration</span></span>

<span data-ttu-id="dcbbe-162">*本部分适用于解决方案的**服务器**应用。*</span><span class="sxs-lookup"><span data-stu-id="dcbbe-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="dcbbe-163">身份验证包</span><span class="sxs-lookup"><span data-stu-id="dcbbe-163">Authentication package</span></span>

<span data-ttu-id="dcbbe-164">`Microsoft.AspNetCore.Authentication.AzureAD.UI`提供对 ASP.NET Core Web Api 的身份验证和授权调用的支持：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="dcbbe-165">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="dcbbe-165">Authentication service support</span></span>

<span data-ttu-id="dcbbe-166">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="dcbbe-167">`AddAzureADBearer` 方法在验证 Azure Active Directory 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="dcbbe-168">`UseAuthentication` 和 `UseAuthorization` 确保：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="dcbbe-169">应用尝试分析和验证传入请求的令牌。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="dcbbe-170">任何试图访问受保护资源的请求均不正确。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="dcbbe-171">应用设置</span><span class="sxs-lookup"><span data-stu-id="dcbbe-171">App settings</span></span>

<span data-ttu-id="dcbbe-172">*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-172">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="dcbbe-173">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="dcbbe-173">WeatherForecast controller</span></span>

<span data-ttu-id="dcbbe-174">WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，并将 `[Authorize]` 特性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-174">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="dcbbe-175">**务必**要了解：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-175">It's **important** to understand that:</span></span>

* <span data-ttu-id="dcbbe-176">此 API 控制器中的 `[Authorize]` 属性是保护此 API 不受未经授权的访问的唯一操作。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-176">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="dcbbe-177">Blazor WebAssembly 应用程序中使用的 `[Authorize]` 属性仅作为对应用程序的提示，用户应授权该应用程序正常工作。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-177">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="dcbbe-178">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="dcbbe-178">Client app configuration</span></span>

<span data-ttu-id="dcbbe-179">*本部分适用于解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="dcbbe-179">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="dcbbe-180">身份验证包</span><span class="sxs-lookup"><span data-stu-id="dcbbe-180">Authentication package</span></span>

<span data-ttu-id="dcbbe-181">创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-181">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="dcbbe-182">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-182">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="dcbbe-183">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-183">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="dcbbe-184">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-184">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="dcbbe-185">`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-185">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="dcbbe-186">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="dcbbe-186">Authentication service support</span></span>

<span data-ttu-id="dcbbe-187">使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-187">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="dcbbe-188">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-188">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="dcbbe-189">Program.cs：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-189">*Program.cs*:</span></span>

<span data-ttu-id="dcbbe-190">生成*客户端应用*时，默认的访问令牌范围为 `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`格式。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-190">When the *Client app* is generated, the default access token scope is of the format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span></span> <span data-ttu-id="dcbbe-191">**删除范围值的 `api://` 部分。**</span><span class="sxs-lookup"><span data-stu-id="dcbbe-191">**Remove the `api://` portion of the scope value.**</span></span> <span data-ttu-id="dcbbe-192">此问题将在将来的预览版本中得到解决。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-192">This issue will be addressed in a future preview release.</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="dcbbe-193">默认访问令牌范围必须是 `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` 格式（例如，`11111111-1111-1111-1111-111111111111/API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-193">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="dcbbe-194">如果向范围设置提供方案或方案和主机（如 Azure 门户中所示），则当*客户端应用*收到来自*服务器 API 应用*的*401 未经授权*响应时，将引发未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-194">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

<span data-ttu-id="dcbbe-195">`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="dcbbe-196">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="dcbbe-197">默认访问令牌范围表示访问令牌作用域的列表：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="dcbbe-198">默认情况下，在登录请求中包括。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="dcbbe-199">用于在身份验证后立即设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="dcbbe-200">对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="dcbbe-201">可以根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="dcbbe-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="dcbbe-202">索引页面</span><span class="sxs-lookup"><span data-stu-id="dcbbe-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="dcbbe-203">应用组件</span><span class="sxs-lookup"><span data-stu-id="dcbbe-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="dcbbe-204">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="dcbbe-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="dcbbe-205">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="dcbbe-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="dcbbe-206">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="dcbbe-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="dcbbe-207">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="dcbbe-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="dcbbe-208">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="dcbbe-208">Run the app</span></span>

<span data-ttu-id="dcbbe-209">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-209">Run the app from the Server project.</span></span> <span data-ttu-id="dcbbe-210">使用 Visual Studio 时，请在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="dcbbe-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="dcbbe-211">其他资源</span><span class="sxs-lookup"><span data-stu-id="dcbbe-211">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
