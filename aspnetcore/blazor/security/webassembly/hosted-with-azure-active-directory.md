---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: 了解如何使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 托管应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/08/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e6f514793a2efde120f70ac58f4ad4be7516ada7
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900829"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="a1d9e-103">使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="a1d9e-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="a1d9e-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a1d9e-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a1d9e-105">本文介绍如何创建使用 [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) 进行身份验证的[托管 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="a1d9e-106">在 AAD 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="a1d9e-106">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a1d9e-107">创建租户</span><span class="sxs-lookup"><span data-stu-id="a1d9e-107">Create a tenant</span></span>

<span data-ttu-id="a1d9e-108">请按照[快速入门：设置租户](/azure/active-directory/develop/quickstart-create-new-tenant)中的指南操作，以在 AAD 中创建租户。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-108">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a1d9e-109">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="a1d9e-109">Register a server API app</span></span>

<span data-ttu-id="a1d9e-110">请按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)中的指南和后续 Azure AAD 主题操作，以便为服务器 API 应用注册 AAD 应用，然后执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-110">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="a1d9e-111">在“Azure Active Directory” > “应用注册”中，选择“新建注册”  。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a1d9e-112">提供应用的名称（例如 Blazor Server AAD） 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-112">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a1d9e-113">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="a1d9e-114">为此体验选择“仅此组织目录中的帐户”（单个租户）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-114">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a1d9e-115">在这种情况下，“服务器 API 应用”不需要“重定向 URI”，因此请将下拉列表设置为 Web，并且不输入重定向 URI 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a1d9e-116">清除“权限” > “授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-116">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a1d9e-117">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-117">Select **Register**.</span></span>

<span data-ttu-id="a1d9e-118">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-118">Record the following information:</span></span>

* <span data-ttu-id="a1d9e-119">“服务器 API 应用”应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="a1d9e-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a1d9e-120">目录（租户）ID（例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`）</span><span class="sxs-lookup"><span data-stu-id="a1d9e-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="a1d9e-121">AAD 主域/发布者域/租户域（例如 `contoso.onmicrosoft.com`）：该域在注册应用的 Azure 门户的“品牌”边栏选项卡中作为“发布者域”提供 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a1d9e-122">在“API 权限”中，删除“Microsoft Graph” > “User.Read”权限，因为应用无需登录或用户配置文件访问权限  。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-122">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="a1d9e-123">在“公开 API”中：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="a1d9e-124">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a1d9e-125">选择“保存并继续”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a1d9e-126">提供“作用域名称”（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a1d9e-127">提供“管理员同意显示名称”（例如 `Access API`）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a1d9e-128">提供“管理员同意说明”（例如 `Allows the app to access server app API endpoints.`）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a1d9e-129">确认“状态”设置为“已启用” 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a1d9e-130">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-130">Select **Add scope**.</span></span>

<span data-ttu-id="a1d9e-131">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-131">Record the following information:</span></span>

* <span data-ttu-id="a1d9e-132">应用 ID URI（例如 `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`、`https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 或你提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="a1d9e-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="a1d9e-133">范围名称（如 `API.Access`）</span><span class="sxs-lookup"><span data-stu-id="a1d9e-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a1d9e-134">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="a1d9e-134">Register a client app</span></span>

<span data-ttu-id="a1d9e-135">请按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)中的指南和后续 Azure AAD 主题操作，以便为 `Client` 应用注册 AAD 应用，然后执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="a1d9e-136">在“Azure Active Directory”>“应用注册”中，选择“新建注册”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a1d9e-137">提供应用的名称（例如 Blazor 客户端 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="a1d9e-138">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="a1d9e-139">为此体验选择“仅此组织目录中的帐户”（单个租户）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a1d9e-140">将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-140">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a1d9e-141">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a1d9e-142">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a1d9e-143">对于 IIS Express，可以在“调试”面板的 `Server` 应用属性中找到应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-143">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a1d9e-144">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a1d9e-145">[创建应用](#create-the-app)部分中会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a1d9e-146">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-146">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a1d9e-147">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-147">Select **Register**.</span></span>

<span data-ttu-id="a1d9e-148">记录 `Client` 应用程序（客户端）ID（例如 `4369008b-21fa-427c-abaa-9b53bf58e538`）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-148">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a1d9e-149">在“身份验证”>“平台配置”>“单页应用程序(SPA)”中：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-149">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="a1d9e-150">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a1d9e-151">对于“隐式授权”，请确保没有选中“访问令牌”和“ID 令牌”的复选框。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-151">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a1d9e-152">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a1d9e-153">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-153">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="a1d9e-154">在“Azure Active Directory”>“应用注册”中，选择“新建注册”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-154">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a1d9e-155">提供应用的名称（例如 Blazor 客户端 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-155">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="a1d9e-156">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-156">Choose a **Supported account types**.</span></span> <span data-ttu-id="a1d9e-157">为此体验选择“仅此组织目录中的帐户”（单个租户）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-157">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a1d9e-158">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-158">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a1d9e-159">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-159">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a1d9e-160">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-160">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a1d9e-161">对于 IIS Express，可以在“调试”面板的 `Server` 应用属性中找到应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-161">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a1d9e-162">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-162">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a1d9e-163">[创建应用](#create-the-app)部分中会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-163">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a1d9e-164">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-164">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a1d9e-165">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-165">Select **Register**.</span></span>

<span data-ttu-id="a1d9e-166">记录 `Client` 应用程序（客户端）ID（例如 `4369008b-21fa-427c-abaa-9b53bf58e538`）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-166">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a1d9e-167">在“身份验证”>“平台配置”>“Web”中：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-167">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a1d9e-168">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-168">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a1d9e-169">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-169">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a1d9e-170">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-170">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a1d9e-171">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-171">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a1d9e-172">在“API 权限”中：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-172">In **API permissions**:</span></span>

1. <span data-ttu-id="a1d9e-173">确认应用拥有“Microsoft Graph” > “User.Read”权限 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-173">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a1d9e-174">选择“添加权限”，然后选择“我的 API” 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-174">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a1d9e-175">从“名称”列（例如 Blazor Server AAD）中选择“服务器 API 应用” 。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-175">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a1d9e-176">打开 API 列表。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-176">Open the **API** list.</span></span>
1. <span data-ttu-id="a1d9e-177">启用对 API 的访问（例如 `API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-177">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a1d9e-178">选择“添加权限”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-178">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a1d9e-179">选择“为 {TENANT NAME} 授予管理员内容”按钮。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-179">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="a1d9e-180">请选择“是”以确认。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-180">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a1d9e-181">创建应用</span><span class="sxs-lookup"><span data-stu-id="a1d9e-181">Create the app</span></span>

<span data-ttu-id="a1d9e-182">在空文件夹中，将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-182">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="a1d9e-183">占位符</span><span class="sxs-lookup"><span data-stu-id="a1d9e-183">Placeholder</span></span>                  | <span data-ttu-id="a1d9e-184">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="a1d9e-184">Azure portal name</span></span>                                     | <span data-ttu-id="a1d9e-185">示例</span><span class="sxs-lookup"><span data-stu-id="a1d9e-185">Example</span></span>                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="a1d9e-186">`Client` 应用的应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="a1d9e-186">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="a1d9e-187">作用域名</span><span class="sxs-lookup"><span data-stu-id="a1d9e-187">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="a1d9e-188">“服务器 API 应用”的应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="a1d9e-188">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="a1d9e-189">应用程序 ID URI</span><span class="sxs-lookup"><span data-stu-id="a1d9e-189">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="a1d9e-190">主域/发布者域/租户域</span><span class="sxs-lookup"><span data-stu-id="a1d9e-190">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | <span data-ttu-id="a1d9e-191">目录（租户）ID</span><span class="sxs-lookup"><span data-stu-id="a1d9e-191">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

<span data-ttu-id="a1d9e-192">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-192">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a1d9e-193">将 Azure 租户与未验证的发布者域一起使用时，可能需要进行配置更改，[应用设置](#app-settings)部分对此进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-193">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a1d9e-194">将 Azure 租户与未验证的发布者域一起使用时，可能需要进行配置更改，[访问令牌作用域](#access-token-scopes)部分对此进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-194">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a1d9e-195">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置 `Client` 应用的平台配置“重定向 URI”。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-195">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a1d9e-196">如果 `Client` 应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的“服务器 API 应用”属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-196">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a1d9e-197">如果端口之前未使用 `Client` 应用的已知端口进行配置，请返回到 Azure 门户中 `Client` 应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-197">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a1d9e-198">`Server` 应用配置</span><span class="sxs-lookup"><span data-stu-id="a1d9e-198">*`Server`* app configuration</span></span>

<span data-ttu-id="a1d9e-199">本部分涉及解决方案的 `Server` 应用\*\*。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-199">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a1d9e-200">身份验证包</span><span class="sxs-lookup"><span data-stu-id="a1d9e-200">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a1d9e-201">以下包提供使用 Microsoft Identity 平台验证和授权对 ASP.NET Core Web API 的调用的支持：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-201">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="a1d9e-202">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 NuGet.org）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-202">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a1d9e-203">[`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) 包提供验证和授权对 ASP.NET Core Web API 的调用的支持：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-203">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="a1d9e-204">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-204">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="a1d9e-205">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="a1d9e-205">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a1d9e-206">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-206">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a1d9e-207"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> 方法将服务配置为使用 Microsoft Identity 平台 v2.0 保护 Web API。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-207">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="a1d9e-208">此方法需要应用配置中的 `AzureAd` 部分通过必要的设置来初始化身份验证选项。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-208">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a1d9e-209">`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-209">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a1d9e-210"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> 方法在 JWT 持有者处理程序中设置验证 Azure Active Directory 发出的令牌所需的特定参数：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-210">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="a1d9e-211"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 和 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 确保：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-211"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="a1d9e-212">应用尝试对传入请求上的令牌进行分析和验证。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-212">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a1d9e-213">尝试在没有适当凭据的情况下访问受保护资源的任何请求都失败。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-213">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="a1d9e-214">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="a1d9e-214">User.Identity.Name</span></span>

<span data-ttu-id="a1d9e-215">默认情况下，`Server` 应用 API 使用 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 声明类型（例如 `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`）中的值填充 `User.Identity.Name`。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-215">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="a1d9e-216">要将应用配置为从 `name` 声明类型接收值，请在 `Startup.ConfigureServices` 中配置 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> 的 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-216">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a1d9e-217">应用设置</span><span class="sxs-lookup"><span data-stu-id="a1d9e-217">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a1d9e-218">`appsettings.json` 文件包含用于配置 JWT 持有者处理程序（用于验证访问令牌）的选项：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-218">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="a1d9e-219">示例：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-219">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a1d9e-220">`appsettings.json` 文件包含用于配置 JWT 持有者处理程序（用于验证访问令牌）的选项：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-220">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="a1d9e-221">示例：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-221">Example:</span></span>

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

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="a1d9e-222">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="a1d9e-222">WeatherForecast controller</span></span>

<span data-ttu-id="a1d9e-223">WeatherForecast 控制器 (Controllers/WeatherForecastController.cs) 公开了一个受保护的 API，并将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性应用于控制器。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-223">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="a1d9e-224">务必了解以下内容：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-224">It's **important** to understand that:</span></span>

* <span data-ttu-id="a1d9e-225">仅此 API 控制器中的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性可以保护此 API 免受未经授权的访问。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-225">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a1d9e-226">Blazor WebAssembly 应用中使用的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性仅用作应用的提示，提示应授权用户应用才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-226">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a1d9e-227">`Client` 应用配置</span><span class="sxs-lookup"><span data-stu-id="a1d9e-227">*`Client`* app configuration</span></span>

<span data-ttu-id="a1d9e-228">本部分涉及解决方案的 `Client` 应用\*\*。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-228">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a1d9e-229">身份验证包</span><span class="sxs-lookup"><span data-stu-id="a1d9e-229">Authentication package</span></span>

<span data-ttu-id="a1d9e-230">创建应用以使用工作或学校帐户 (`SingleOrg`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-230">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a1d9e-231">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-231">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a1d9e-232">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-232">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a1d9e-233">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-233">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a1d9e-234">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-234">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a1d9e-235">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="a1d9e-235">Authentication service support</span></span>

<span data-ttu-id="a1d9e-236">添加了对 <xref:System.Net.Http.HttpClient> 实例的支持，这些实例在向服务器项目发出请求时包含访问令牌。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-236">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a1d9e-237">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a1d9e-237">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a1d9e-238">占位符 `{APP ASSEMBLY}` 是应用的程序集名称（例如 `BlazorSample.ServerAPI`）。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-238">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="a1d9e-239">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-239">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a1d9e-240">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-240">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a1d9e-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a1d9e-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a1d9e-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-242">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a1d9e-243">注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-243">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a1d9e-244">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-244">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a1d9e-245">示例：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-245">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a1d9e-246">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="a1d9e-246">Access token scopes</span></span>

<span data-ttu-id="a1d9e-247">默认访问令牌作用域表示访问令牌作用域的列表，这些作用域是：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-247">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a1d9e-248">默认情况下包含在登录请求中。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-248">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a1d9e-249">用于在身份验证后立即预配访问令牌。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-249">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a1d9e-250">根据 Azure Active Directory 规则，所有作用域必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-250">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a1d9e-251">根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-251">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a1d9e-252">使用 `AdditionalScopesToConsent` 指定其他作用域：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-252">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="a1d9e-253">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-253">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a1d9e-254">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="a1d9e-254">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a1d9e-255">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="a1d9e-255">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="a1d9e-256">登录模式</span><span class="sxs-lookup"><span data-stu-id="a1d9e-256">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="a1d9e-257">导入文件</span><span class="sxs-lookup"><span data-stu-id="a1d9e-257">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a1d9e-258">索引页</span><span class="sxs-lookup"><span data-stu-id="a1d9e-258">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a1d9e-259">应用组件</span><span class="sxs-lookup"><span data-stu-id="a1d9e-259">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a1d9e-260">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="a1d9e-260">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a1d9e-261">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="a1d9e-261">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a1d9e-262">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="a1d9e-262">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a1d9e-263">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="a1d9e-263">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a1d9e-264">运行应用</span><span class="sxs-lookup"><span data-stu-id="a1d9e-264">Run the app</span></span>

<span data-ttu-id="a1d9e-265">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-265">Run the app from the Server project.</span></span> <span data-ttu-id="a1d9e-266">使用 Visual Studio 时，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="a1d9e-266">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a1d9e-267">在工具栏中将“启动项目”下拉列表设置为“服务器 API 应用”，然后选择“运行”按钮。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-267">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a1d9e-268">在“解决方案资源管理器”中选择服务器项目，然后选择工具栏中的“运行”按钮，或从“调试”菜单启动应用  。</span><span class="sxs-lookup"><span data-stu-id="a1d9e-268">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a1d9e-269">其他资源</span><span class="sxs-lookup"><span data-stu-id="a1d9e-269">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a1d9e-270">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="a1d9e-270">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a1d9e-271">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="a1d9e-271">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
