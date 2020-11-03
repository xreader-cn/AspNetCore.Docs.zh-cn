---
title: '使用 Azure Active Directory 保护 ASP.NET Core :::no-loc(Blazor WebAssembly)::: 独立应用'
author: guardrex
description: '了解如何使用 Azure Active Directory 保护 ASP.NET Core :::no-loc(Blazor WebAssembly)::: 独立应用。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 46e5a422864dd8f6aef72afddb3b406bc99f9163
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690421"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="e7fdb-103">使用 Azure Active Directory 保护 ASP.NET Core :::no-loc(Blazor WebAssembly)::: 独立应用</span><span class="sxs-lookup"><span data-stu-id="e7fdb-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Azure Active Directory</span></span>

<span data-ttu-id="e7fdb-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e7fdb-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e7fdb-105">本文介绍如何使用 Azure Active Directory (AAD) 保护 ASP.NET Core :::no-loc(Blazor WebAssembly)::: 独立应用。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-105">This article covers how to secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Azure Active Directory (AAD).</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="e7fdb-106">对于在 Visual Studio 中创建且配置为支持 AAD 组织目录中帐户的 :::no-loc(Blazor WebAssembly)::: 应用，Visual Studio 不会在项目生成时正确配置应用。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-106">For :::no-loc(Blazor WebAssembly)::: apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="e7fdb-107">以后的 Visual Studio 版本将解决此问题。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="e7fdb-108">本文演示如何使用 .NET Core CLI 的 `dotnet new` 命令创建应用。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="e7fdb-109">如果希望在针对 ASP.NET Core 5.0 中的最新 :::no-loc(Blazor)::: 模板更新 IDE 之前使用 Visual Studio 创建应用，请参阅本文的各个部分，并在 Visual Studio 创建应用后确认或更新应用的配置。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest :::no-loc(Blazor)::: templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

<span data-ttu-id="e7fdb-110">若要创建使用 [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) 进行身份验证的[独立 :::no-loc(Blazor WebAssembly)::: 应用](xref:blazor/hosting-models#blazor-webassembly)，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-110">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="e7fdb-111">[创建 AAD 租户和 Web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-111">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="e7fdb-112">在 Azure 门户的“Azure Active Directory” > “应用注册”区域中注册 AAD 应用 ：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-112">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="e7fdb-113">提供应用的名称（例如 :::no-loc(Blazor)::: 独立 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-113">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD** ).</span></span>
1. <span data-ttu-id="e7fdb-114">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="e7fdb-115">为此体验选择“仅此组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-115">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="e7fdb-116">将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-116">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e7fdb-117">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-117">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e7fdb-118">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-118">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e7fdb-119">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-119">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e7fdb-120">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-120">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e7fdb-121">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-121">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e7fdb-122">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-122">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e7fdb-123">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-123">Select **Register**.</span></span>

<span data-ttu-id="e7fdb-124">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-124">Record the following information:</span></span>

* <span data-ttu-id="e7fdb-125">应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="e7fdb-125">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="e7fdb-126">目录（租户）ID（例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`）</span><span class="sxs-lookup"><span data-stu-id="e7fdb-126">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="e7fdb-127">在“身份验证”>“平台配置”>“单页应用程序(SPA)”中：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-127">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="e7fdb-128">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-128">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e7fdb-129">对于“隐式授权”，请确保没有选中“访问令牌”和“ID 令牌”的复选框。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-129">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="e7fdb-130">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-130">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e7fdb-131">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-131">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="e7fdb-132">提供应用的名称（例如 :::no-loc(Blazor)::: 独立 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-132">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD** ).</span></span>
1. <span data-ttu-id="e7fdb-133">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-133">Choose a **Supported account types**.</span></span> <span data-ttu-id="e7fdb-134">为此体验选择“仅此组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-134">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="e7fdb-135">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-135">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e7fdb-136">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-136">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e7fdb-137">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-137">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e7fdb-138">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-138">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e7fdb-139">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-139">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e7fdb-140">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-140">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e7fdb-141">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-141">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e7fdb-142">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-142">Select **Register**.</span></span>

<span data-ttu-id="e7fdb-143">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-143">Record the following information:</span></span>

* <span data-ttu-id="e7fdb-144">应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="e7fdb-144">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="e7fdb-145">目录（租户）ID（例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`）</span><span class="sxs-lookup"><span data-stu-id="e7fdb-145">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="e7fdb-146">在“身份验证”>“平台配置”>“Web”中：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-146">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="e7fdb-147">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-147">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e7fdb-148">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-148">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e7fdb-149">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-149">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e7fdb-150">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-150">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="e7fdb-151">在空文件夹中创建应用。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-151">Create the app in an empty folder.</span></span> <span data-ttu-id="e7fdb-152">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-152">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="e7fdb-153">占位符</span><span class="sxs-lookup"><span data-stu-id="e7fdb-153">Placeholder</span></span>   | <span data-ttu-id="e7fdb-154">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="e7fdb-154">Azure portal name</span></span>       | <span data-ttu-id="e7fdb-155">示例</span><span class="sxs-lookup"><span data-stu-id="e7fdb-155">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `:::no-loc(Blazor):::Sample`                         |
| `{CLIENT ID}` | <span data-ttu-id="e7fdb-156">应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="e7fdb-156">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="e7fdb-157">目录（租户）ID</span><span class="sxs-lookup"><span data-stu-id="e7fdb-157">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="e7fdb-158">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e7fdb-159">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置应用的平台配置“重定向 URI”。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="e7fdb-160">如果应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的应用属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="e7fdb-161">如果端口之前未使用应用的已知端口进行配置，请返回到 Azure 门户中应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="e7fdb-162">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e7fdb-163">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="e7fdb-164">请求 Microsoft API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="e7fdb-165">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-165">For more information, see:</span></span>
  * [<span data-ttu-id="e7fdb-166">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="e7fdb-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="e7fdb-167">[快速入门：配置应用程序来公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e7fdb-168">身份验证包</span><span class="sxs-lookup"><span data-stu-id="e7fdb-168">Authentication package</span></span>

<span data-ttu-id="e7fdb-169">创建应用以使用工作或学校帐户 (`SingleOrg`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-169">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="e7fdb-170">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e7fdb-171">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="e7fdb-172">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="e7fdb-173">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e7fdb-174">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="e7fdb-174">Authentication service support</span></span>

<span data-ttu-id="e7fdb-175">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="e7fdb-176">此方法设置应用与 :::no-loc(Identity)::: 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-176">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="e7fdb-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="e7fdb-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="e7fdb-178"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e7fdb-179">注册应用时，可以从 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="e7fdb-180">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-180">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="e7fdb-181">示例：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-181">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="e7fdb-182">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="e7fdb-182">Access token scopes</span></span>

<span data-ttu-id="e7fdb-183">:::no-loc(Blazor WebAssembly)::: 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="e7fdb-183">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e7fdb-184">要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="e7fdb-185">使用 `AdditionalScopesToConsent` 指定其他作用域：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="e7fdb-186">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="e7fdb-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e7fdb-187">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="e7fdb-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e7fdb-188">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="e7fdb-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="e7fdb-189">登录模式</span><span class="sxs-lookup"><span data-stu-id="e7fdb-189">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="e7fdb-190">导入文件</span><span class="sxs-lookup"><span data-stu-id="e7fdb-190">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e7fdb-191">索引页</span><span class="sxs-lookup"><span data-stu-id="e7fdb-191">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e7fdb-192">应用组件</span><span class="sxs-lookup"><span data-stu-id="e7fdb-192">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e7fdb-193">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="e7fdb-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e7fdb-194">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="e7fdb-194">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e7fdb-195">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="e7fdb-195">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e7fdb-196">其他资源</span><span class="sxs-lookup"><span data-stu-id="e7fdb-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="e7fdb-197">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="e7fdb-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="e7fdb-198">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="e7fdb-198">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
