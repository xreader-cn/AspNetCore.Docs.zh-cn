---
title: 使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: cf5e89c5f89fbf156d1f0d5751d3ff519bde7c8f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626306"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="47a58-102">使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="47a58-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="47a58-103">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47a58-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="47a58-104">本文介绍如何创建使用 [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) 进行身份验证的[托管 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="47a58-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="47a58-105">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="47a58-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="47a58-106">创建租户</span><span class="sxs-lookup"><span data-stu-id="47a58-106">Create a tenant</span></span>

<span data-ttu-id="47a58-107">请按照[教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)里的指南创建 AAD B2C 租户。</span><span class="sxs-lookup"><span data-stu-id="47a58-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="47a58-108">记录 AAD B2C 实例（例如 `https://contoso.b2clogin.com/`，其中包含尾部斜杠）。</span><span class="sxs-lookup"><span data-stu-id="47a58-108">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="47a58-109">此实例是 Azure B2C 应用注册的方案和宿主，可以通过从 Azure 门户的“应用注册”页打开“终结点”窗口找到它。 </span><span class="sxs-lookup"><span data-stu-id="47a58-109">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="47a58-110">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="47a58-110">Register a server API app</span></span>

<span data-ttu-id="47a58-111">请按照[教程：在 Azure Active Directory B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)中的指南，为服务器 API 应用注册 AAD 应用，然后执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="47a58-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="47a58-112">在“Azure Active Directory” > “应用注册”中，选择“新建注册”  。</span><span class="sxs-lookup"><span data-stu-id="47a58-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="47a58-113">提供应用的名称（例如 Blazor Server AAD B2C） 。</span><span class="sxs-lookup"><span data-stu-id="47a58-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="47a58-114">对于“支持的帐户类型”，请选择多租户选项：任何组织目录或任何标识提供者中的帐户。用于使用 Azure AD B2C 对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="47a58-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="47a58-115">在这种情况下，“服务器 API 应用”不需要“重定向 URI”，因此请将下拉列表设置为 Web，并且不输入重定向 URI 。</span><span class="sxs-lookup"><span data-stu-id="47a58-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="47a58-116">确认已启用“权限” > “授予对 openid 和 offline_access 权限的管理员同意” 。</span><span class="sxs-lookup"><span data-stu-id="47a58-116">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="47a58-117">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="47a58-117">Select **Register**.</span></span>

<span data-ttu-id="47a58-118">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="47a58-118">Record the following information:</span></span>

* <span data-ttu-id="47a58-119">“服务器 API 应用”应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="47a58-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="47a58-120">AAD 主域/发布者域/租户域（例如 `contoso.onmicrosoft.com`）：该域在注册应用的 Azure 门户的“品牌”边栏选项卡中作为“发布者域”提供 。</span><span class="sxs-lookup"><span data-stu-id="47a58-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="47a58-121">在“公开 API”中：</span><span class="sxs-lookup"><span data-stu-id="47a58-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="47a58-122">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="47a58-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="47a58-123">选择“保存并继续”。</span><span class="sxs-lookup"><span data-stu-id="47a58-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="47a58-124">提供“作用域名称”（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="47a58-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="47a58-125">提供“管理员同意显示名称”（例如 `Access API`）。</span><span class="sxs-lookup"><span data-stu-id="47a58-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="47a58-126">提供“管理员同意说明”（例如 `Allows the app to access server app API endpoints.`）。</span><span class="sxs-lookup"><span data-stu-id="47a58-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="47a58-127">确认“状态”设置为“已启用” 。</span><span class="sxs-lookup"><span data-stu-id="47a58-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="47a58-128">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="47a58-128">Select **Add scope**.</span></span>

<span data-ttu-id="47a58-129">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="47a58-129">Record the following information:</span></span>

* <span data-ttu-id="47a58-130">应用 ID URI（例如 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`、`api://41451fa7-82d9-4673-8fa5-69eff5a761fd` 或你提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="47a58-130">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="47a58-131">范围名称（例如 `API.Access`）</span><span class="sxs-lookup"><span data-stu-id="47a58-131">Scope name (for example, `API.Access`)</span></span>

<span data-ttu-id="47a58-132">应用 ID URI 可能需要在客户端应用中进行专门的配置，如本主题的后续部分[访问令牌作用域](#access-token-scopes)中所述。</span><span class="sxs-lookup"><span data-stu-id="47a58-132">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="47a58-133">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="47a58-133">Register a client app</span></span>

<span data-ttu-id="47a58-134">请按照[教程：在 Azure Active Directory B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)中的指南操作，再次为客户端应用注册 AAD 应用，然后执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="47a58-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="47a58-135">在“Azure Active Directory” > “应用注册”中，选择“新建注册”  。</span><span class="sxs-lookup"><span data-stu-id="47a58-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="47a58-136">提供应用的“名称”（例如 Blazor 客户端 AAD B2C） 。</span><span class="sxs-lookup"><span data-stu-id="47a58-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="47a58-137">对于“支持的帐户类型”，请选择多租户选项：任何组织目录或任何标识提供者中的帐户。用于使用 Azure AD B2C 对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="47a58-137">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="47a58-138">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="47a58-138">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="47a58-139">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="47a58-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="47a58-140">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="47a58-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="47a58-141">对于 IIS Express，可以在“调试”面板的服务器应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="47a58-141">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="47a58-142">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="47a58-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="47a58-143">[创建应用](#create-the-app)部分中会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="47a58-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="47a58-144">确认已启用“权限” > “授予对 openid 和 offline_access 权限的管理员同意” 。</span><span class="sxs-lookup"><span data-stu-id="47a58-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="47a58-145">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="47a58-145">Select **Register**.</span></span>

<span data-ttu-id="47a58-146">记录应用程序（客户端）ID（例如 `4369008b-21fa-427c-abaa-9b53bf58e538`）。</span><span class="sxs-lookup"><span data-stu-id="47a58-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="47a58-147">在“身份验证” > “平台配置” > “Web”  中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="47a58-147">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="47a58-148">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="47a58-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="47a58-149">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="47a58-149">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="47a58-150">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="47a58-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="47a58-151">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="47a58-151">Select the **Save** button.</span></span>

<span data-ttu-id="47a58-152">在“API 权限”中：</span><span class="sxs-lookup"><span data-stu-id="47a58-152">In **API permissions**:</span></span>

1. <span data-ttu-id="47a58-153">选择“添加权限”，然后选择“我的 API” 。</span><span class="sxs-lookup"><span data-stu-id="47a58-153">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="47a58-154">从“名称”列（例如 Blazor Server AAD B2C）中选择“服务器 API 应用” 。</span><span class="sxs-lookup"><span data-stu-id="47a58-154">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="47a58-155">打开 API 列表。</span><span class="sxs-lookup"><span data-stu-id="47a58-155">Open the **API** list.</span></span>
1. <span data-ttu-id="47a58-156">启用对 API 的访问（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="47a58-156">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="47a58-157">选择“添加权限”。</span><span class="sxs-lookup"><span data-stu-id="47a58-157">Select **Add permissions**.</span></span>
1. <span data-ttu-id="47a58-158">选择“为 {TENANT NAME} 授予管理员内容”按钮。</span><span class="sxs-lookup"><span data-stu-id="47a58-158">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="47a58-159">请选择“是”以确认。</span><span class="sxs-lookup"><span data-stu-id="47a58-159">Select **Yes** to confirm.</span></span>

<span data-ttu-id="47a58-160">在“主页” > “Azure AD B2C” > “用户流”中  ：</span><span class="sxs-lookup"><span data-stu-id="47a58-160">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="47a58-161">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="47a58-161">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="47a58-162">至少选择“应用程序声明” > “显示名称”用户属性以填充 `LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 中的 `context.User.Identity.Name` 。</span><span class="sxs-lookup"><span data-stu-id="47a58-162">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="47a58-163">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin`）。</span><span class="sxs-lookup"><span data-stu-id="47a58-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="47a58-164">创建应用</span><span class="sxs-lookup"><span data-stu-id="47a58-164">Create the app</span></span>

<span data-ttu-id="47a58-165">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="47a58-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="47a58-166">占位符</span><span class="sxs-lookup"><span data-stu-id="47a58-166">Placeholder</span></span>                   | <span data-ttu-id="47a58-167">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="47a58-167">Azure portal name</span></span>                                     | <span data-ttu-id="47a58-168">示例</span><span class="sxs-lookup"><span data-stu-id="47a58-168">Example</span></span>                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="47a58-169">实例</span><span class="sxs-lookup"><span data-stu-id="47a58-169">Instance</span></span>                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="47a58-170">“客户端应用”的应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="47a58-170">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="47a58-171">作用域名</span><span class="sxs-lookup"><span data-stu-id="47a58-171">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="47a58-172">“服务器 API 应用”的应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="47a58-172">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="47a58-173">应用程序 ID URI（[请参阅说明](#access-token-scopes)）</span><span class="sxs-lookup"><span data-stu-id="47a58-173">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="47a58-174">注册/登录用户流</span><span class="sxs-lookup"><span data-stu-id="47a58-174">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="47a58-175">主域/发布者域/租户域</span><span class="sxs-lookup"><span data-stu-id="47a58-175">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |

<span data-ttu-id="47a58-176">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="47a58-176">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="47a58-177">将应用 ID URI 传递给 `app-id-uri` 选项，但请注意，客户端应用中可能需要更改配置，如[访问令牌作用域](#access-token-scopes)部分中所述。</span><span class="sxs-lookup"><span data-stu-id="47a58-177">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="47a58-178">此外，由托管 Blazor 模板设置的作用域可能会重复应用 ID URI 主机。</span><span class="sxs-lookup"><span data-stu-id="47a58-178">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="47a58-179">确认为 `DefaultAccessTokenScopes` 集合配置的作用域在客户端应用的 `Program.Main` (`Program.cs`) 中是正确的。</span><span class="sxs-lookup"><span data-stu-id="47a58-179">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="47a58-180">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置了客户端应用的“身份验证” > “平台配置” > “Web” > “重定向 URI”   。</span><span class="sxs-lookup"><span data-stu-id="47a58-180">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="47a58-181">如果“客户端应用”是在随机 IIS Express 端口上运行的，则可以在“调试”面板的“服务器 API 应用”属性中找到该应用的端口 。</span><span class="sxs-lookup"><span data-stu-id="47a58-181">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="47a58-182">如果端口之前未使用“客户端应用”的已知端口进行配置，请返回到 Azure 门户中“客户端应用”的注册，并使用正确的端口更新重定向 URI 。</span><span class="sxs-lookup"><span data-stu-id="47a58-182">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="47a58-183">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="47a58-183">Server app configuration</span></span>

<span data-ttu-id="47a58-184">本部分涉及解决方案的 `Server` 应用\*\*。</span><span class="sxs-lookup"><span data-stu-id="47a58-184">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="47a58-185">身份验证包</span><span class="sxs-lookup"><span data-stu-id="47a58-185">Authentication package</span></span>

<span data-ttu-id="47a58-186">[`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) 包提供验证和授权对 ASP.NET Core Web API 的调用的支持：</span><span class="sxs-lookup"><span data-stu-id="47a58-186">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="47a58-187">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="47a58-187">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="47a58-188">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="47a58-188">Authentication service support</span></span>

<span data-ttu-id="47a58-189">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="47a58-189">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="47a58-190"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> 方法在 JWT 持有者处理程序中设置验证 Azure Active Directory B2C 发出的令牌所需的特定参数：</span><span class="sxs-lookup"><span data-stu-id="47a58-190">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="47a58-191"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 和 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 确保：</span><span class="sxs-lookup"><span data-stu-id="47a58-191"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="47a58-192">应用尝试对传入请求上的令牌进行分析和验证。</span><span class="sxs-lookup"><span data-stu-id="47a58-192">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="47a58-193">尝试在没有适当凭据的情况下访问受保护资源的任何请求都失败。</span><span class="sxs-lookup"><span data-stu-id="47a58-193">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="47a58-194">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="47a58-194">User.Identity.Name</span></span>

<span data-ttu-id="47a58-195">默认情况下，不填充 `User.Identity.Name`。</span><span class="sxs-lookup"><span data-stu-id="47a58-195">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="47a58-196">要将应用配置为从 `name` 声明类型接收值，请在 `Startup.ConfigureServices` 中配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 的 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="47a58-196">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="47a58-197">应用设置</span><span class="sxs-lookup"><span data-stu-id="47a58-197">App settings</span></span>

<span data-ttu-id="47a58-198">`appsettings.json` 文件包含用于配置 JWT 持有者处理程序（用于验证访问令牌）的选项。</span><span class="sxs-lookup"><span data-stu-id="47a58-198">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="47a58-199">示例：</span><span class="sxs-lookup"><span data-stu-id="47a58-199">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="47a58-200">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="47a58-200">WeatherForecast controller</span></span>

<span data-ttu-id="47a58-201">WeatherForecast 控制器 (Controllers/WeatherForecastController.cs) 公开了一个受保护的 API，并将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性应用于控制器。</span><span class="sxs-lookup"><span data-stu-id="47a58-201">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="47a58-202">务必了解以下内容：</span><span class="sxs-lookup"><span data-stu-id="47a58-202">It's **important** to understand that:</span></span>

* <span data-ttu-id="47a58-203">仅此 API 控制器中的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性可以保护此 API 免受未经授权的访问。</span><span class="sxs-lookup"><span data-stu-id="47a58-203">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="47a58-204">Blazor WebAssembly 应用中使用的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性仅用作应用的提示，提示应授权用户应用才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="47a58-204">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="47a58-205">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="47a58-205">Client app configuration</span></span>

<span data-ttu-id="47a58-206">本部分涉及解决方案的 `Client` 应用\*\*。</span><span class="sxs-lookup"><span data-stu-id="47a58-206">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="47a58-207">身份验证包</span><span class="sxs-lookup"><span data-stu-id="47a58-207">Authentication package</span></span>

<span data-ttu-id="47a58-208">创建应用以使用个人 B2C 帐户 (`IndividualB2C`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="47a58-208">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="47a58-209">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="47a58-209">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="47a58-210">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="47a58-210">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="47a58-211">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="47a58-211">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="47a58-212">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="47a58-212">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="47a58-213">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="47a58-213">Authentication service support</span></span>

<span data-ttu-id="47a58-214">添加了对 <xref:System.Net.Http.HttpClient> 实例的支持，这些实例在向服务器项目发出请求时包含访问令牌。</span><span class="sxs-lookup"><span data-stu-id="47a58-214">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="47a58-215">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="47a58-215">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="47a58-216">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `BlazorSample.ServerAPI`）。</span><span class="sxs-lookup"><span data-stu-id="47a58-216">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="47a58-217">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="47a58-217">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="47a58-218">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="47a58-218">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="47a58-219">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="47a58-219">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="47a58-220"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="47a58-220">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="47a58-221">注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="47a58-221">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="47a58-222">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="47a58-222">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="47a58-223">示例：</span><span class="sxs-lookup"><span data-stu-id="47a58-223">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="47a58-224">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="47a58-224">Access token scopes</span></span>

<span data-ttu-id="47a58-225">默认访问令牌作用域表示访问令牌作用域的列表，这些作用域是：</span><span class="sxs-lookup"><span data-stu-id="47a58-225">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="47a58-226">默认情况下包含在登录请求中。</span><span class="sxs-lookup"><span data-stu-id="47a58-226">Included by default in the sign in request.</span></span>
* <span data-ttu-id="47a58-227">用于在身份验证后立即预配访问令牌。</span><span class="sxs-lookup"><span data-stu-id="47a58-227">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="47a58-228">根据 Azure Active Directory 规则，所有作用域必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="47a58-228">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="47a58-229">根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="47a58-229">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="47a58-230">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="47a58-230">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="47a58-231">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="47a58-231">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="47a58-232">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="47a58-232">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="47a58-233">登录模式</span><span class="sxs-lookup"><span data-stu-id="47a58-233">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="47a58-234">导入文件</span><span class="sxs-lookup"><span data-stu-id="47a58-234">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="47a58-235">索引页</span><span class="sxs-lookup"><span data-stu-id="47a58-235">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="47a58-236">应用组件</span><span class="sxs-lookup"><span data-stu-id="47a58-236">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="47a58-237">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="47a58-237">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="47a58-238">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="47a58-238">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="47a58-239">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="47a58-239">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="47a58-240">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="47a58-240">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="47a58-241">运行应用</span><span class="sxs-lookup"><span data-stu-id="47a58-241">Run the app</span></span>

<span data-ttu-id="47a58-242">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="47a58-242">Run the app from the Server project.</span></span> <span data-ttu-id="47a58-243">使用 Visual Studio 时，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="47a58-243">When using Visual Studio, either:</span></span>

* <span data-ttu-id="47a58-244">在工具栏中将“启动项目”下拉列表设置为“服务器 API 应用”，然后选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="47a58-244">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="47a58-245">在“解决方案资源管理器”中选择服务器项目，然后选择工具栏中的“运行”按钮，或从“调试”菜单启动应用  。</span><span class="sxs-lookup"><span data-stu-id="47a58-245">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="47a58-246">其他资源</span><span class="sxs-lookup"><span data-stu-id="47a58-246">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="47a58-247">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="47a58-247">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="47a58-248">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="47a58-248">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="47a58-249">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="47a58-249">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
