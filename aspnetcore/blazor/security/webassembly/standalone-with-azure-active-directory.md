---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 1852ff5637cff9aef9ad713dc470ea0354b3fb47
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243429"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="64e28-102">使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="64e28-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="64e28-103">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="64e28-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="64e28-104">若要创建使用 [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) 进行身份验证的 Blazor WebAssembly 独立应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="64e28-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="64e28-105">[创建 AAD 租户和 Web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="64e28-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="64e28-106">在 Azure 门户的“Azure Active Directory” > “应用注册”区域中注册 AAD 应用 ：</span><span class="sxs-lookup"><span data-stu-id="64e28-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="64e28-107">提供应用的名称（例如 Blazor 独立 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="64e28-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="64e28-108">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="64e28-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="64e28-109">为此体验选择“仅此组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="64e28-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="64e28-110">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="64e28-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="64e28-111">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="64e28-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="64e28-112">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="64e28-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="64e28-113">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="64e28-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="64e28-114">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="64e28-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="64e28-115">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="64e28-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="64e28-116">禁用“权限” > “授予对 openid 和 offline_access 权限的管理员同意”复选框 。</span><span class="sxs-lookup"><span data-stu-id="64e28-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="64e28-117">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="64e28-117">Select **Register**.</span></span>

<span data-ttu-id="64e28-118">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="64e28-118">Record the following information:</span></span>

* <span data-ttu-id="64e28-119">应用程序 ID（客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="64e28-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="64e28-120">目录 ID（租户 ID）（例如 `22222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="64e28-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="64e28-121">在“身份验证” > “平台配置” > “Web”  中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="64e28-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="64e28-122">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="64e28-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="64e28-123">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="64e28-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="64e28-124">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="64e28-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="64e28-125">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="64e28-125">Select the **Save** button.</span></span>

<span data-ttu-id="64e28-126">创建应用。</span><span class="sxs-lookup"><span data-stu-id="64e28-126">Create the app.</span></span> <span data-ttu-id="64e28-127">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="64e28-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="64e28-128">要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径（例如 `-o BlazorSample`）的输出选项。</span><span class="sxs-lookup"><span data-stu-id="64e28-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="64e28-129">该文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="64e28-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="64e28-130">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置应用的“身份验证” > “平台配置” > “Web” > “重定向 URI”   。</span><span class="sxs-lookup"><span data-stu-id="64e28-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="64e28-131">如果应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的应用属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="64e28-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="64e28-132">如果端口之前未使用应用的已知端口进行配置，请返回到 Azure 门户中应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="64e28-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="64e28-133">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="64e28-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="64e28-134">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="64e28-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="64e28-135">请求 Microsoft API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="64e28-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="64e28-136">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="64e28-136">For more information, see:</span></span>
  * [<span data-ttu-id="64e28-137">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="64e28-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="64e28-138">[快速入门：配置应用程序来公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="64e28-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="64e28-139">身份验证包</span><span class="sxs-lookup"><span data-stu-id="64e28-139">Authentication package</span></span>

<span data-ttu-id="64e28-140">创建应用以使用工作或学校帐户 (`SingleOrg`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="64e28-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="64e28-141">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="64e28-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="64e28-142">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="64e28-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="64e28-143">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="64e28-143">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="64e28-144">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="64e28-144">Authentication service support</span></span>

<span data-ttu-id="64e28-145">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="64e28-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="64e28-146">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="64e28-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="64e28-147">`Program.cs`：</span><span class="sxs-lookup"><span data-stu-id="64e28-147">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="64e28-148"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="64e28-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="64e28-149">注册应用时，可以从 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="64e28-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="64e28-150">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="64e28-150">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="64e28-151">示例：</span><span class="sxs-lookup"><span data-stu-id="64e28-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="64e28-152">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="64e28-152">Access token scopes</span></span>

<span data-ttu-id="64e28-153">Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="64e28-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="64e28-154">要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="64e28-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="64e28-155">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="64e28-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="64e28-156">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="64e28-156">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="64e28-157">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="64e28-157">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="64e28-158">导入文件</span><span class="sxs-lookup"><span data-stu-id="64e28-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="64e28-159">索引页</span><span class="sxs-lookup"><span data-stu-id="64e28-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="64e28-160">应用组件</span><span class="sxs-lookup"><span data-stu-id="64e28-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="64e28-161">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="64e28-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="64e28-162">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="64e28-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="64e28-163">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="64e28-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="64e28-164">其他资源</span><span class="sxs-lookup"><span data-stu-id="64e28-164">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="64e28-165">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="64e28-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="64e28-166">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="64e28-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
