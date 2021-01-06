---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: 了解如何使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 557c700ffd24d4d6c85848ad360690d583e05321
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854466"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="be8c6-103">使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="be8c6-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="be8c6-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="be8c6-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="be8c6-105">本文介绍如何创建使用 [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) 进行身份验证的[独立 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="be8c6-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="be8c6-106">对于在 Visual Studio 中创建且配置为支持 AAD 组织目录中帐户的 Blazor WebAssembly 应用，Visual Studio 当前不会在项目生成时正确配置 Azure 门户应用注册。</span><span class="sxs-lookup"><span data-stu-id="be8c6-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="be8c6-107">以后的 Visual Studio 版本将解决此问题。</span><span class="sxs-lookup"><span data-stu-id="be8c6-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="be8c6-108">本文介绍如何使用 .NET CLI `dotnet new` 命令创建解决方案和 Azure 应用门户注册，以及在 Azure 门户中手动创建应用注册。</span><span class="sxs-lookup"><span data-stu-id="be8c6-108">This article shows how to create the solution and Azure app portal registration with the .NET CLI `dotnet new` command and by manually creating the app registration in the Azure portal.</span></span>
>
> <span data-ttu-id="be8c6-109">如果希望在更新 IDE 之前使用 Visual Studio 创建解决方案和 Azure 应用注册，请参阅本文的各个部分，并在 Visual Studio 创建解决方案后确认或更新应用的配置和应用的注册。</span><span class="sxs-lookup"><span data-stu-id="be8c6-109">If you prefer to create the solution and Azure app registration with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the app's configuration and the app's registration after Visual Studio creates the solution.</span></span>

<span data-ttu-id="be8c6-110">在 Azure 门户的“Azure Active Directory”>“应用注册”区域中注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="be8c6-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="be8c6-111">提供应用的名称（例如 Blazor 独立 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="be8c6-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="be8c6-112">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="be8c6-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="be8c6-113">为此体验选择“仅此组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="be8c6-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="be8c6-114">将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="be8c6-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="be8c6-115">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="be8c6-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="be8c6-116">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="be8c6-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="be8c6-117">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="be8c6-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="be8c6-118">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="be8c6-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="be8c6-119">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="be8c6-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="be8c6-120">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="be8c6-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="be8c6-121">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="be8c6-121">Select **Register**.</span></span>

<span data-ttu-id="be8c6-122">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="be8c6-122">Record the following information:</span></span>

* <span data-ttu-id="be8c6-123">应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="be8c6-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="be8c6-124">目录（租户）ID（例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`）</span><span class="sxs-lookup"><span data-stu-id="be8c6-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="be8c6-125">在“身份验证”>“平台配置”>“单页应用程序(SPA)”中：</span><span class="sxs-lookup"><span data-stu-id="be8c6-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="be8c6-126">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="be8c6-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="be8c6-127">对于“隐式授权”，请确保没有选中“访问令牌”和“ID 令牌”的复选框。</span><span class="sxs-lookup"><span data-stu-id="be8c6-127">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="be8c6-128">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="be8c6-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="be8c6-129">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="be8c6-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="be8c6-130">在 Azure 门户的“Azure Active Directory”>“应用注册”区域中注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="be8c6-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="be8c6-131">提供应用的名称（例如 Blazor 独立 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="be8c6-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="be8c6-132">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="be8c6-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="be8c6-133">为此体验选择“仅此组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="be8c6-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="be8c6-134">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="be8c6-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="be8c6-135">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="be8c6-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="be8c6-136">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="be8c6-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="be8c6-137">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="be8c6-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="be8c6-138">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="be8c6-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="be8c6-139">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="be8c6-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="be8c6-140">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="be8c6-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="be8c6-141">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="be8c6-141">Select **Register**.</span></span>

<span data-ttu-id="be8c6-142">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="be8c6-142">Record the following information:</span></span>

* <span data-ttu-id="be8c6-143">应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="be8c6-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="be8c6-144">目录（租户）ID（例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`）</span><span class="sxs-lookup"><span data-stu-id="be8c6-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="be8c6-145">在“身份验证”>“平台配置”>“Web”中：</span><span class="sxs-lookup"><span data-stu-id="be8c6-145">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="be8c6-146">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="be8c6-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="be8c6-147">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="be8c6-147">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="be8c6-148">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="be8c6-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="be8c6-149">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="be8c6-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="be8c6-150">在空文件夹中创建应用。</span><span class="sxs-lookup"><span data-stu-id="be8c6-150">Create the app in an empty folder.</span></span> <span data-ttu-id="be8c6-151">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="be8c6-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="be8c6-152">占位符</span><span class="sxs-lookup"><span data-stu-id="be8c6-152">Placeholder</span></span>   | <span data-ttu-id="be8c6-153">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="be8c6-153">Azure portal name</span></span>       | <span data-ttu-id="be8c6-154">示例</span><span class="sxs-lookup"><span data-stu-id="be8c6-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="be8c6-155">应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="be8c6-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="be8c6-156">目录（租户）ID</span><span class="sxs-lookup"><span data-stu-id="be8c6-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="be8c6-157">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="be8c6-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="be8c6-158">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置应用的平台配置“重定向 URI”。</span><span class="sxs-lookup"><span data-stu-id="be8c6-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="be8c6-159">如果应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的应用属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="be8c6-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="be8c6-160">如果端口之前未使用应用的已知端口进行配置，请返回到 Azure 门户中应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="be8c6-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="be8c6-161">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="be8c6-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="be8c6-162">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="be8c6-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="be8c6-163">请求 Microsoft API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="be8c6-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="be8c6-164">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="be8c6-164">For more information, see:</span></span>
  * [<span data-ttu-id="be8c6-165">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="be8c6-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="be8c6-166">[快速入门：配置应用程序来公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="be8c6-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="be8c6-167">身份验证包</span><span class="sxs-lookup"><span data-stu-id="be8c6-167">Authentication package</span></span>

<span data-ttu-id="be8c6-168">创建应用以使用工作或学校帐户 (`SingleOrg`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="be8c6-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="be8c6-169">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="be8c6-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="be8c6-170">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="be8c6-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="be8c6-171">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="be8c6-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="be8c6-172">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="be8c6-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="be8c6-173">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="be8c6-173">Authentication service support</span></span>

<span data-ttu-id="be8c6-174">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="be8c6-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="be8c6-175">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="be8c6-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="be8c6-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="be8c6-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="be8c6-177"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="be8c6-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="be8c6-178">注册应用时，可以从 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="be8c6-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="be8c6-179">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="be8c6-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="be8c6-180">示例：</span><span class="sxs-lookup"><span data-stu-id="be8c6-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="be8c6-181">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="be8c6-181">Access token scopes</span></span>

<span data-ttu-id="be8c6-182">Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="be8c6-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="be8c6-183">要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="be8c6-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="be8c6-184">使用 `AdditionalScopesToConsent` 指定其他作用域：</span><span class="sxs-lookup"><span data-stu-id="be8c6-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="be8c6-185">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="be8c6-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="be8c6-186">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="be8c6-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="be8c6-187">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="be8c6-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="be8c6-188">登录模式</span><span class="sxs-lookup"><span data-stu-id="be8c6-188">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="be8c6-189">导入文件</span><span class="sxs-lookup"><span data-stu-id="be8c6-189">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="be8c6-190">索引页</span><span class="sxs-lookup"><span data-stu-id="be8c6-190">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="be8c6-191">应用组件</span><span class="sxs-lookup"><span data-stu-id="be8c6-191">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="be8c6-192">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="be8c6-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="be8c6-193">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="be8c6-193">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="be8c6-194">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="be8c6-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="be8c6-195">其他资源</span><span class="sxs-lookup"><span data-stu-id="be8c6-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="be8c6-196">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="be8c6-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="be8c6-197">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="be8c6-197">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
