---
title: 使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: 了解如何使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: cddde7d3125dba1a250563085c366122eb26e509
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280909"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="a6d72-103">使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="a6d72-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="a6d72-104">本文介绍如何创建使用[带有 Azure Active Directory (AAD) 的 Microsoft 帐户](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)进行身份验证的[独立 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="a6d72-104">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="a6d72-105">在 Azure 门户的“Azure Active Directory” > “应用注册”区域中注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="a6d72-105">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="a6d72-106">提供应用的名称（例如 Blazor 独立 AAD Microsoft 帐户） 。</span><span class="sxs-lookup"><span data-stu-id="a6d72-106">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="a6d72-107">在“支持的帐户类型”中，选择“任何组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="a6d72-107">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="a6d72-108">将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="a6d72-108">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a6d72-109">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="a6d72-109">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a6d72-110">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="a6d72-110">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a6d72-111">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="a6d72-111">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a6d72-112">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a6d72-112">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a6d72-113">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a6d72-113">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a6d72-114">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="a6d72-114">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a6d72-115">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="a6d72-115">Select **Register**.</span></span>

<span data-ttu-id="a6d72-116">记录应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）。</span><span class="sxs-lookup"><span data-stu-id="a6d72-116">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="a6d72-117">在“身份验证”>“平台配置”>“单页应用程序(SPA)”中：</span><span class="sxs-lookup"><span data-stu-id="a6d72-117">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="a6d72-118">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a6d72-118">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a6d72-119">对于“隐式授权”，请确保没有选中“访问令牌”和“ID 令牌”的复选框。</span><span class="sxs-lookup"><span data-stu-id="a6d72-119">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a6d72-120">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="a6d72-120">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a6d72-121">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="a6d72-121">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="a6d72-122">提供应用的名称（例如 Blazor 独立 AAD Microsoft 帐户） 。</span><span class="sxs-lookup"><span data-stu-id="a6d72-122">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="a6d72-123">在“支持的帐户类型”中，选择“任何组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="a6d72-123">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="a6d72-124">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="a6d72-124">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a6d72-125">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="a6d72-125">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a6d72-126">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="a6d72-126">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a6d72-127">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="a6d72-127">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a6d72-128">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a6d72-128">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a6d72-129">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a6d72-129">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a6d72-130">取消选中“权限”>“授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="a6d72-130">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a6d72-131">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="a6d72-131">Select **Register**.</span></span>

<span data-ttu-id="a6d72-132">记录应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）。</span><span class="sxs-lookup"><span data-stu-id="a6d72-132">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="a6d72-133">在“身份验证”>“平台配置”>“Web”中：</span><span class="sxs-lookup"><span data-stu-id="a6d72-133">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a6d72-134">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a6d72-134">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a6d72-135">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="a6d72-135">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a6d72-136">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="a6d72-136">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a6d72-137">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="a6d72-137">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a6d72-138">创建应用。</span><span class="sxs-lookup"><span data-stu-id="a6d72-138">Create the app.</span></span> <span data-ttu-id="a6d72-139">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a6d72-139">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="a6d72-140">占位符</span><span class="sxs-lookup"><span data-stu-id="a6d72-140">Placeholder</span></span>   | <span data-ttu-id="a6d72-141">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="a6d72-141">Azure portal name</span></span>       | <span data-ttu-id="a6d72-142">示例</span><span class="sxs-lookup"><span data-stu-id="a6d72-142">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="a6d72-143">应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="a6d72-143">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="a6d72-144">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="a6d72-144">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a6d72-145">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置应用的平台配置“重定向 URI”。</span><span class="sxs-lookup"><span data-stu-id="a6d72-145">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a6d72-146">如果应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的应用属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="a6d72-146">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a6d72-147">如果端口之前未使用应用的已知端口进行配置，请返回到 Azure 门户中应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="a6d72-147">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="a6d72-148">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="a6d72-148">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="a6d72-149">使用 Microsoft 帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="a6d72-149">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="a6d72-150">请求 Microsoft API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="a6d72-150">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="a6d72-151">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="a6d72-151">For more information, see:</span></span>
  * [<span data-ttu-id="a6d72-152">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="a6d72-152">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="a6d72-153">[快速入门：配置应用程序来公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="a6d72-153">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a6d72-154">身份验证包</span><span class="sxs-lookup"><span data-stu-id="a6d72-154">Authentication package</span></span>

<span data-ttu-id="a6d72-155">创建应用以使用工作或学校帐户 (`SingleOrg`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="a6d72-155">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a6d72-156">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="a6d72-156">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a6d72-157">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="a6d72-157">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a6d72-158">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="a6d72-158">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a6d72-159">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="a6d72-159">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a6d72-160">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="a6d72-160">Authentication service support</span></span>

<span data-ttu-id="a6d72-161">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="a6d72-161">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a6d72-162">此方法设置应用与 Identity 提供者 (IP) 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="a6d72-162">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a6d72-163">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a6d72-163">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="a6d72-164"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="a6d72-164">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a6d72-165">注册应用时，可以从 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="a6d72-165">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="a6d72-166">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="a6d72-166">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a6d72-167">示例：</span><span class="sxs-lookup"><span data-stu-id="a6d72-167">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="a6d72-168">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="a6d72-168">Access token scopes</span></span>

<span data-ttu-id="a6d72-169">Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="a6d72-169">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a6d72-170">要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="a6d72-170">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a6d72-171">使用 `AdditionalScopesToConsent` 指定其他作用域：</span><span class="sxs-lookup"><span data-stu-id="a6d72-171">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="a6d72-172">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="a6d72-172">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a6d72-173">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="a6d72-173">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a6d72-174">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="a6d72-174">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="a6d72-175">登录模式</span><span class="sxs-lookup"><span data-stu-id="a6d72-175">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="a6d72-176">导入文件</span><span class="sxs-lookup"><span data-stu-id="a6d72-176">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a6d72-177">索引页</span><span class="sxs-lookup"><span data-stu-id="a6d72-177">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a6d72-178">应用组件</span><span class="sxs-lookup"><span data-stu-id="a6d72-178">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a6d72-179">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="a6d72-179">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a6d72-180">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="a6d72-180">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a6d72-181">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="a6d72-181">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a6d72-182">其他资源</span><span class="sxs-lookup"><span data-stu-id="a6d72-182">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a6d72-183">生成 Authentication.MSAL JavaScript 库的自定义版本</span><span class="sxs-lookup"><span data-stu-id="a6d72-183">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="a6d72-184">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="a6d72-184">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="a6d72-185">快速入门：将应用程序注册到 Microsoft 标识平台</span><span class="sxs-lookup"><span data-stu-id="a6d72-185">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="a6d72-186">快速入门：配置应用程序以公开 Web API</span><span class="sxs-lookup"><span data-stu-id="a6d72-186">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
