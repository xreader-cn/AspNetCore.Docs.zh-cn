---
title: 使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: 了解如何使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 1c87330dec069e05f274206d2d35f50f489f9623
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710615"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="da90c-103">使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="da90c-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="da90c-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="da90c-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="da90c-105">本文介绍如何创建使用 [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) 进行身份验证的[托管 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="da90c-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="da90c-106">在 AAD B2C 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="da90c-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="da90c-107">创建租户</span><span class="sxs-lookup"><span data-stu-id="da90c-107">Create a tenant</span></span>

<span data-ttu-id="da90c-108">请按照[教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)里的指南创建 AAD B2C 租户。</span><span class="sxs-lookup"><span data-stu-id="da90c-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="da90c-109">创建或确定要使用的租户后立即返回到本文。</span><span class="sxs-lookup"><span data-stu-id="da90c-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="da90c-110">记录 AAD B2C 实例（例如 `https://contoso.b2clogin.com/`，其中包含尾部斜杠）。</span><span class="sxs-lookup"><span data-stu-id="da90c-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="da90c-111">此实例是 Azure B2C 应用注册的方案和宿主，可以通过从 Azure 门户的“应用注册”页打开“终结点”窗口找到它。 </span><span class="sxs-lookup"><span data-stu-id="da90c-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="da90c-112">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="da90c-112">Register a server API app</span></span>

<span data-ttu-id="da90c-113">为服务器 API 应用注册 AAD B2C 应用：</span><span class="sxs-lookup"><span data-stu-id="da90c-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="da90c-114">在“Azure Active Directory” > “应用注册”中，选择“新建注册”  。</span><span class="sxs-lookup"><span data-stu-id="da90c-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="da90c-115">提供应用的名称（例如 Blazor Server AAD B2C） 。</span><span class="sxs-lookup"><span data-stu-id="da90c-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="da90c-116">对于“支持的帐户类型”，请选择多租户选项：任何标识提供程序或组织目录中的帐户（用于通过用户流对用户进行身份验证）</span><span class="sxs-lookup"><span data-stu-id="da90c-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="da90c-117">在这种情况下，“服务器 API 应用”不需要“重定向 URI”，因此请将下拉列表设置为 Web，并且不输入重定向 URI 。</span><span class="sxs-lookup"><span data-stu-id="da90c-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="da90c-118">确认已选择“权限” > “授予对 openid 和 offline_access 权限的管理员同意”。</span><span class="sxs-lookup"><span data-stu-id="da90c-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="da90c-119">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="da90c-119">Select **Register**.</span></span>

<span data-ttu-id="da90c-120">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="da90c-120">Record the following information:</span></span>

* <span data-ttu-id="da90c-121">“服务器 API 应用”应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="da90c-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="da90c-122">AAD 主域/发布者域/租户域（例如 `contoso.onmicrosoft.com`）：该域在注册应用的 Azure 门户的“品牌”边栏选项卡中作为“发布者域”提供 。</span><span class="sxs-lookup"><span data-stu-id="da90c-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="da90c-123">在“公开 API”中：</span><span class="sxs-lookup"><span data-stu-id="da90c-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="da90c-124">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="da90c-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="da90c-125">选择“保存并继续”。</span><span class="sxs-lookup"><span data-stu-id="da90c-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="da90c-126">提供“作用域名称”（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="da90c-127">提供“管理员同意显示名称”（例如 `Access API`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="da90c-128">提供“管理员同意说明”（例如 `Allows the app to access server app API endpoints.`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="da90c-129">确认“状态”设置为“已启用” 。</span><span class="sxs-lookup"><span data-stu-id="da90c-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="da90c-130">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="da90c-130">Select **Add scope**.</span></span>

<span data-ttu-id="da90c-131">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="da90c-131">Record the following information:</span></span>

* <span data-ttu-id="da90c-132">应用 ID URI（例如 `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`、`https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 或你提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="da90c-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="da90c-133">范围名称（如 `API.Access`）</span><span class="sxs-lookup"><span data-stu-id="da90c-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="da90c-134">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="da90c-134">Register a client app</span></span>

<span data-ttu-id="da90c-135">为客户端应用注册 AAD B2C 应用：</span><span class="sxs-lookup"><span data-stu-id="da90c-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="da90c-136">在“Azure Active Directory”>“应用注册”中，选择“新建注册”。</span><span class="sxs-lookup"><span data-stu-id="da90c-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="da90c-137">提供应用的“名称”（例如 Blazor 客户端 AAD B2C） 。</span><span class="sxs-lookup"><span data-stu-id="da90c-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="da90c-138">对于“支持的帐户类型”，请选择多租户选项：任何标识提供程序或组织目录中的帐户（用于通过用户流对用户进行身份验证）</span><span class="sxs-lookup"><span data-stu-id="da90c-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="da90c-139">将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="da90c-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="da90c-140">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="da90c-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="da90c-141">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="da90c-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="da90c-142">对于 IIS Express，可以在“调试”面板的 `Server` 应用属性中找到应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="da90c-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="da90c-143">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da90c-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="da90c-144">[创建应用](#create-the-app)部分中会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da90c-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="da90c-145">确认已选择“权限”>“授予对 openid 和 offline_access 权限的管理员同意”。</span><span class="sxs-lookup"><span data-stu-id="da90c-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="da90c-146">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="da90c-146">Select **Register**.</span></span>

1. <span data-ttu-id="da90c-147">记录应用程序（客户端）ID（例如 `4369008b-21fa-427c-abaa-9b53bf58e538`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="da90c-148">在“身份验证”>“平台配置”>“单页应用程序(SPA)”中：</span><span class="sxs-lookup"><span data-stu-id="da90c-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="da90c-149">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da90c-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="da90c-150">对于“隐式授权”，请确保没有选中“访问令牌”和“ID 令牌”的复选框。</span><span class="sxs-lookup"><span data-stu-id="da90c-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="da90c-151">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="da90c-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="da90c-152">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="da90c-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="da90c-153">在“Azure Active Directory”>“应用注册”中，选择“新建注册”。</span><span class="sxs-lookup"><span data-stu-id="da90c-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="da90c-154">提供应用的“名称”（例如 Blazor 客户端 AAD B2C） 。</span><span class="sxs-lookup"><span data-stu-id="da90c-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="da90c-155">对于“支持的帐户类型”，请选择多租户选项：任何标识提供程序或组织目录中的帐户（用于通过用户流对用户进行身份验证）</span><span class="sxs-lookup"><span data-stu-id="da90c-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="da90c-156">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="da90c-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="da90c-157">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="da90c-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="da90c-158">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="da90c-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="da90c-159">对于 IIS Express，可以在“调试”面板的 `Server` 应用属性中找到应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="da90c-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="da90c-160">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da90c-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="da90c-161">[创建应用](#create-the-app)部分中会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da90c-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="da90c-162">确认已选择“权限”>“授予对 openid 和 offline_access 权限的管理员同意”。</span><span class="sxs-lookup"><span data-stu-id="da90c-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="da90c-163">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="da90c-163">Select **Register**.</span></span>

<span data-ttu-id="da90c-164">记录应用程序（客户端）ID（例如 `4369008b-21fa-427c-abaa-9b53bf58e538`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="da90c-165">在“身份验证”>“平台配置”>“Web”中：</span><span class="sxs-lookup"><span data-stu-id="da90c-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="da90c-166">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da90c-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="da90c-167">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="da90c-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="da90c-168">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="da90c-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="da90c-169">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="da90c-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="da90c-170">在“API 权限”中：</span><span class="sxs-lookup"><span data-stu-id="da90c-170">In **API permissions**:</span></span>

1. <span data-ttu-id="da90c-171">选择“添加权限”，然后选择“我的 API” 。</span><span class="sxs-lookup"><span data-stu-id="da90c-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="da90c-172">从“名称”列（例如 Blazor Server AAD B2C）中选择“服务器 API 应用” 。</span><span class="sxs-lookup"><span data-stu-id="da90c-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="da90c-173">打开 API 列表。</span><span class="sxs-lookup"><span data-stu-id="da90c-173">Open the **API** list.</span></span>
1. <span data-ttu-id="da90c-174">启用对 API 的访问（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="da90c-175">选择“添加权限”。</span><span class="sxs-lookup"><span data-stu-id="da90c-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="da90c-176">选择“为 {TENANT NAME} 授予管理员内容”按钮。</span><span class="sxs-lookup"><span data-stu-id="da90c-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="da90c-177">请选择“是”以确认。</span><span class="sxs-lookup"><span data-stu-id="da90c-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="da90c-178">在“主页” > “Azure AD B2C” > “用户流”中  ：</span><span class="sxs-lookup"><span data-stu-id="da90c-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="da90c-179">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="da90c-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="da90c-180">至少选择“应用程序声明” > “显示名称”用户属性以填充 `LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 中的 `context.User.Identity.Name` 。</span><span class="sxs-lookup"><span data-stu-id="da90c-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="da90c-181">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="da90c-182">创建应用</span><span class="sxs-lookup"><span data-stu-id="da90c-182">Create the app</span></span>

<span data-ttu-id="da90c-183">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="da90c-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="da90c-184">占位符</span><span class="sxs-lookup"><span data-stu-id="da90c-184">Placeholder</span></span>                   | <span data-ttu-id="da90c-185">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="da90c-185">Azure portal name</span></span>                                     | <span data-ttu-id="da90c-186">示例</span><span class="sxs-lookup"><span data-stu-id="da90c-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="da90c-187">实例</span><span class="sxs-lookup"><span data-stu-id="da90c-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="da90c-188">`Client` 应用的应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="da90c-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="da90c-189">作用域名</span><span class="sxs-lookup"><span data-stu-id="da90c-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="da90c-190">“服务器 API 应用”的应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="da90c-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="da90c-191">应用程序 ID URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="da90c-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="da90c-192">注册/登录用户流</span><span class="sxs-lookup"><span data-stu-id="da90c-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="da90c-193">主域/发布者域/租户域</span><span class="sxs-lookup"><span data-stu-id="da90c-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="da90c-194">&dagger;Blazor WebAssembly 模板会自动将 `api://` 的方案添加到 `dotnet new` 命令中传递的应用 ID URI 参数。</span><span class="sxs-lookup"><span data-stu-id="da90c-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="da90c-195">为 `{SERVER API APP ID URI}` 占位符提供应用 ID URI 时，如果方案为 `api://`，请从参数中删除方案 (`api://`)，如上表中的示例值所示。</span><span class="sxs-lookup"><span data-stu-id="da90c-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="da90c-196">如果应用 ID URI 是一个自定义值，或者有一些其他方案（例如，不受信任的发布者域的 `https://` 类似于 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`），则必须手动更新默认范围 URI，并在模板创建 `Client` 应用后删除 `api://` 方案。</span><span class="sxs-lookup"><span data-stu-id="da90c-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="da90c-197">有关详细信息，请参阅[访问令牌范围](#access-token-scopes)部分中的说明。</span><span class="sxs-lookup"><span data-stu-id="da90c-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="da90c-198">Blazor WebAssembly 模板可能会在未来版本的 ASP.NET Core 中有所更改来解决这些方案。</span><span class="sxs-lookup"><span data-stu-id="da90c-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="da90c-199">有关详细信息，请参阅[结合使用应用 ID URI 与 Blazor WASM 模板的双方案（托管、单组织）(dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417)。</span><span class="sxs-lookup"><span data-stu-id="da90c-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="da90c-200">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="da90c-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="da90c-201">由托管 Blazor 模板设置的作用域可能会重复应用 ID URI 主机。</span><span class="sxs-lookup"><span data-stu-id="da90c-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="da90c-202">确认为 `DefaultAccessTokenScopes` 集合配置的作用域在 `Client` 应用的 `Program.Main` (`Program.cs`) 中是正确的。</span><span class="sxs-lookup"><span data-stu-id="da90c-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="da90c-203">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置 `Client` 应用的平台配置“重定向 URI”。</span><span class="sxs-lookup"><span data-stu-id="da90c-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="da90c-204">如果 `Client` 应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的“服务器 API 应用”属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="da90c-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="da90c-205">如果端口之前未使用 `Client` 应用的已知端口进行配置，请返回到 Azure 门户中 `Client` 应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="da90c-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="da90c-206">`Server` 应用配置</span><span class="sxs-lookup"><span data-stu-id="da90c-206">*`Server`* app configuration</span></span>

<span data-ttu-id="da90c-207">本部分涉及解决方案的 `Server` 应用。</span><span class="sxs-lookup"><span data-stu-id="da90c-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="da90c-208">身份验证包</span><span class="sxs-lookup"><span data-stu-id="da90c-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="da90c-209">以下包提供使用 Microsoft Identity 平台验证和授权对 ASP.NET Core Web API 的调用的支持：</span><span class="sxs-lookup"><span data-stu-id="da90c-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="da90c-210">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 NuGet.org）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="da90c-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="da90c-211">[`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) 包提供验证和授权对 ASP.NET Core Web API 的调用的支持：</span><span class="sxs-lookup"><span data-stu-id="da90c-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="da90c-212">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="da90c-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="da90c-213">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="da90c-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="da90c-214">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="da90c-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="da90c-215"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> 方法将服务配置为使用 Microsoft Identity 平台 v2.0 保护 Web API。</span><span class="sxs-lookup"><span data-stu-id="da90c-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="da90c-216">此方法需要应用配置中的 `AzureAdB2C` 部分通过必要的设置来初始化身份验证选项。</span><span class="sxs-lookup"><span data-stu-id="da90c-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="da90c-217">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="da90c-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="da90c-218"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> 方法在 JWT 持有者处理程序中设置验证 Azure Active Directory B2C 发出的令牌所需的特定参数：</span><span class="sxs-lookup"><span data-stu-id="da90c-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="da90c-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 和 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 确保：</span><span class="sxs-lookup"><span data-stu-id="da90c-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="da90c-220">应用尝试对传入请求上的令牌进行分析和验证。</span><span class="sxs-lookup"><span data-stu-id="da90c-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="da90c-221">尝试在没有适当凭据的情况下访问受保护资源的任何请求都失败。</span><span class="sxs-lookup"><span data-stu-id="da90c-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="da90c-222">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="da90c-222">User.Identity.Name</span></span>

<span data-ttu-id="da90c-223">默认情况下，不填充 `User.Identity.Name`。</span><span class="sxs-lookup"><span data-stu-id="da90c-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="da90c-224">要将应用配置为从 `name` 声明类型接收值，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="da90c-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="da90c-225">向 `Startup.cs` 添加 <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> 的命名空间：</span><span class="sxs-lookup"><span data-stu-id="da90c-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="da90c-226">在 `Startup.ConfigureServices` 中配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 的 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="da90c-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="da90c-227">在 `Startup.ConfigureServices` 中配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 的 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="da90c-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="da90c-228">应用设置</span><span class="sxs-lookup"><span data-stu-id="da90c-228">App settings</span></span>

<span data-ttu-id="da90c-229">`appsettings.json` 文件包含用于配置 JWT 持有者处理程序（用于验证访问令牌）的选项。</span><span class="sxs-lookup"><span data-stu-id="da90c-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="da90c-230">示例：</span><span class="sxs-lookup"><span data-stu-id="da90c-230">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="da90c-231">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="da90c-231">WeatherForecast controller</span></span>

<span data-ttu-id="da90c-232">WeatherForecast 控制器 (Controllers/WeatherForecastController.cs) 公开了一个受保护的 API，并将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性应用于控制器。</span><span class="sxs-lookup"><span data-stu-id="da90c-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="da90c-233">务必了解以下内容：</span><span class="sxs-lookup"><span data-stu-id="da90c-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="da90c-234">仅此 API 控制器中的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性可以保护此 API 免受未经授权的访问。</span><span class="sxs-lookup"><span data-stu-id="da90c-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="da90c-235">Blazor WebAssembly 应用中使用的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性仅用作应用的提示，提示应授权用户应用才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="da90c-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="da90c-236">`Client` 应用配置</span><span class="sxs-lookup"><span data-stu-id="da90c-236">*`Client`* app configuration</span></span>

<span data-ttu-id="da90c-237">本部分涉及解决方案的 `Client` 应用。</span><span class="sxs-lookup"><span data-stu-id="da90c-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="da90c-238">身份验证包</span><span class="sxs-lookup"><span data-stu-id="da90c-238">Authentication package</span></span>

<span data-ttu-id="da90c-239">创建应用以使用个人 B2C 帐户 (`IndividualB2C`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="da90c-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="da90c-240">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="da90c-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="da90c-241">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="da90c-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="da90c-242">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="da90c-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="da90c-243">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="da90c-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="da90c-244">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="da90c-244">Authentication service support</span></span>

<span data-ttu-id="da90c-245">添加了对 <xref:System.Net.Http.HttpClient> 实例的支持，这些实例在向服务器项目发出请求时包含访问令牌。</span><span class="sxs-lookup"><span data-stu-id="da90c-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="da90c-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="da90c-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="da90c-247">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `BlazorSample.ServerAPI`）。</span><span class="sxs-lookup"><span data-stu-id="da90c-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="da90c-248">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="da90c-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="da90c-249">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="da90c-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="da90c-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="da90c-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="da90c-251"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="da90c-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="da90c-252">注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="da90c-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="da90c-253">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="da90c-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="da90c-254">示例：</span><span class="sxs-lookup"><span data-stu-id="da90c-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="da90c-255">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="da90c-255">Access token scopes</span></span>

<span data-ttu-id="da90c-256">默认访问令牌作用域表示访问令牌作用域的列表，这些作用域是：</span><span class="sxs-lookup"><span data-stu-id="da90c-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="da90c-257">默认情况下包含在登录请求中。</span><span class="sxs-lookup"><span data-stu-id="da90c-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="da90c-258">用于在身份验证后立即预配访问令牌。</span><span class="sxs-lookup"><span data-stu-id="da90c-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="da90c-259">根据 Azure Active Directory 规则，所有作用域必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="da90c-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="da90c-260">根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="da90c-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="da90c-261">Blazor WebAssembly 模板会自动将 `api://` 的方案添加到 `dotnet new` 命令中传递的应用 ID URI 参数。</span><span class="sxs-lookup"><span data-stu-id="da90c-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="da90c-262">从 Blazor 项目模板生成应用时，请确认默认访问令牌范围的值使用在 Azure 门户中提供的正确的自定义应用 ID URI 值，或采用以下格式之一的值：</span><span class="sxs-lookup"><span data-stu-id="da90c-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="da90c-263">当目录的发布者域受信任时，默认访问令牌范围通常为类似于以下示例的值，其中 `API.Access` 为默认范围名称：</span><span class="sxs-lookup"><span data-stu-id="da90c-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="da90c-264">检查双方案 (`api://api://...`) 的值。</span><span class="sxs-lookup"><span data-stu-id="da90c-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="da90c-265">如果存在双方案，则从值中删除第一个 `api://` 方案。</span><span class="sxs-lookup"><span data-stu-id="da90c-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="da90c-266">当目录的发布者域不受信任时，默认访问令牌范围通常为类似于以下示例的值，其中 `API.Access` 为默认范围名称：</span><span class="sxs-lookup"><span data-stu-id="da90c-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="da90c-267">检查附加 `api://` 方案 (`api://https://contoso.onmicrosoft.com/...`) 的值。</span><span class="sxs-lookup"><span data-stu-id="da90c-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="da90c-268">如果存在附加 `api://` 方案，则从值中删除 `api://` 方案。</span><span class="sxs-lookup"><span data-stu-id="da90c-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="da90c-269">Blazor WebAssembly 模板可能会在未来版本的 ASP.NET Core 中有所更改来解决这些方案。</span><span class="sxs-lookup"><span data-stu-id="da90c-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="da90c-270">有关详细信息，请参阅[结合使用应用 ID URI 与 Blazor WASM 模板的双方案（托管、单组织）(dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417)。</span><span class="sxs-lookup"><span data-stu-id="da90c-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="da90c-271">使用 `AdditionalScopesToConsent` 指定其他作用域：</span><span class="sxs-lookup"><span data-stu-id="da90c-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="da90c-272">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="da90c-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="da90c-273">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="da90c-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="da90c-274">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="da90c-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="da90c-275">登录模式</span><span class="sxs-lookup"><span data-stu-id="da90c-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="da90c-276">导入文件</span><span class="sxs-lookup"><span data-stu-id="da90c-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="da90c-277">索引页</span><span class="sxs-lookup"><span data-stu-id="da90c-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="da90c-278">应用组件</span><span class="sxs-lookup"><span data-stu-id="da90c-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="da90c-279">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="da90c-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="da90c-280">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="da90c-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="da90c-281">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="da90c-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="da90c-282">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="da90c-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="da90c-283">运行应用</span><span class="sxs-lookup"><span data-stu-id="da90c-283">Run the app</span></span>

<span data-ttu-id="da90c-284">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="da90c-284">Run the app from the Server project.</span></span> <span data-ttu-id="da90c-285">使用 Visual Studio 时，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="da90c-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="da90c-286">在工具栏中将“启动项目”下拉列表设置为“服务器 API 应用”，然后选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="da90c-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="da90c-287">在“解决方案资源管理器”中选择服务器项目，然后选择工具栏中的“运行”按钮，或从“调试”菜单启动应用  。</span><span class="sxs-lookup"><span data-stu-id="da90c-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="da90c-288">其他资源</span><span class="sxs-lookup"><span data-stu-id="da90c-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="da90c-289">生成 Authentication.MSAL JavaScript 库的自定义版本</span><span class="sxs-lookup"><span data-stu-id="da90c-289">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="da90c-290">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="da90c-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="da90c-291">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="da90c-291">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="da90c-292">教程：在 Azure Active Directory B2C 中注册应用程序</span><span class="sxs-lookup"><span data-stu-id="da90c-292">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="da90c-293">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="da90c-293">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
