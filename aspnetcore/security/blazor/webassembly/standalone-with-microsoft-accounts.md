---
title: Blazor使用 Microsoft 帐户保护 ASP.NET Core WebAssembly 独立应用程序
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
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 113a0aaba89b90922a9ccd58da13aabeecec0081
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724310"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="33a9b-102">Blazor使用 Microsoft 帐户保护 ASP.NET Core WebAssembly 独立应用程序</span><span class="sxs-lookup"><span data-stu-id="33a9b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="33a9b-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="33a9b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="33a9b-104">若要创建将 Blazor Microsoft 帐户用于身份验证[AZURE ACTIVE DIRECTORY （AAD）](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)的 WebAssembly 独立应用程序，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="33a9b-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="33a9b-105">创建 AAD 租户和 web 应用程序</span><span class="sxs-lookup"><span data-stu-id="33a9b-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="33a9b-106">在 Azure 门户的**Azure Active Directory**  >  **应用注册**区域中注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="33a9b-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="33a9b-107">提供应用的**名称**（例如， \*\* Blazor 独立 AAD Microsoft 帐户\*\*）。</span><span class="sxs-lookup"><span data-stu-id="33a9b-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="33a9b-108">在 "**支持的帐户类型**" 中，选择**任何组织目录中的帐户**。</span><span class="sxs-lookup"><span data-stu-id="33a9b-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="33a9b-109">将 "**重定向 uri** " 下拉菜单保留设置为 " **Web** "，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="33a9b-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="33a9b-110">在 Kestrel 上运行的应用的默认端口为5001。</span><span class="sxs-lookup"><span data-stu-id="33a9b-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="33a9b-111">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="33a9b-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="33a9b-112">对于 IIS Express，可以在 "**调试**" 面板的应用程序属性中找到应用程序的随机生成端口。</span><span class="sxs-lookup"><span data-stu-id="33a9b-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="33a9b-113">由于此时应用不存在，并且 IIS Express 端口未知，因此在创建应用后返回到此步骤并更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="33a9b-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="33a9b-114">本主题稍后会显示一个批注，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="33a9b-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="33a9b-115">禁用 " **Permissions**将  >  **管理员同意授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="33a9b-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="33a9b-116">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="33a9b-116">Select **Register**.</span></span>

<span data-ttu-id="33a9b-117">记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111` ）。</span><span class="sxs-lookup"><span data-stu-id="33a9b-117">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="33a9b-118">在 "**身份验证**  >  **平台配置**"  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="33a9b-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="33a9b-119">确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="33a9b-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="33a9b-120">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="33a9b-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="33a9b-121">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="33a9b-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="33a9b-122">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="33a9b-122">Select the **Save** button.</span></span>

<span data-ttu-id="33a9b-123">创建应用。</span><span class="sxs-lookup"><span data-stu-id="33a9b-123">Create the app.</span></span> <span data-ttu-id="33a9b-124">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="33a9b-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="33a9b-125">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="33a9b-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="33a9b-126">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="33a9b-126">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="33a9b-127">在 Azure 门户中，将为在**Authentication**  >  **Platform configurations**  >  **Web**  >  Kestrel 服务器上运行的应用配置默认设置，为端口5001配置应用的身份验证平台配置 Web**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="33a9b-127">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="33a9b-128">如果应用在随机 IIS Express 端口上运行，则可在 "**调试**" 面板的应用属性中找到应用的端口。</span><span class="sxs-lookup"><span data-stu-id="33a9b-128">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="33a9b-129">如果先前未将此端口配置为应用的已知端口，请返回到 Azure 门户中的应用注册，并更新具有正确端口的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="33a9b-129">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="33a9b-130">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="33a9b-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="33a9b-131">使用 Microsoft 帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="33a9b-131">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="33a9b-132">请求 Microsoft Api 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="33a9b-132">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="33a9b-133">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="33a9b-133">For more information, see:</span></span>
  * [<span data-ttu-id="33a9b-134">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="33a9b-134">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="33a9b-135">[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="33a9b-135">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="33a9b-136">身份验证包</span><span class="sxs-lookup"><span data-stu-id="33a9b-136">Authentication package</span></span>

<span data-ttu-id="33a9b-137">创建应用以使用工作或学校帐户（ `SingleOrg` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)）的包引用。</span><span class="sxs-lookup"><span data-stu-id="33a9b-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="33a9b-138">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="33a9b-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="33a9b-139">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="33a9b-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="33a9b-140">[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包可向应用程序中添加[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包，并将其添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="33a9b-140">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="33a9b-141">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="33a9b-141">Authentication service support</span></span>

<span data-ttu-id="33a9b-142">使用 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="33a9b-142">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="33a9b-143">此方法设置应用与 Identity 提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="33a9b-143">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="33a9b-144">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="33a9b-144">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="33a9b-145"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="33a9b-145">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="33a9b-146">注册应用时，可以从 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="33a9b-146">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="33a9b-147">配置由文件上的*wwwroot/appsettings.js*提供：</span><span class="sxs-lookup"><span data-stu-id="33a9b-147">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="33a9b-148">示例：</span><span class="sxs-lookup"><span data-stu-id="33a9b-148">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="33a9b-149">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="33a9b-149">Access token scopes</span></span>

<span data-ttu-id="33a9b-150">BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="33a9b-150">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="33a9b-151">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="33a9b-151">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="33a9b-152">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="33a9b-152">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="33a9b-153">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="33a9b-153">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="33a9b-154">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="33a9b-154">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="33a9b-155">导入文件</span><span class="sxs-lookup"><span data-stu-id="33a9b-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="33a9b-156">索引页面</span><span class="sxs-lookup"><span data-stu-id="33a9b-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="33a9b-157">应用组件</span><span class="sxs-lookup"><span data-stu-id="33a9b-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="33a9b-158">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="33a9b-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="33a9b-159">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="33a9b-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="33a9b-160">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="33a9b-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="33a9b-161">其他资源</span><span class="sxs-lookup"><span data-stu-id="33a9b-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="33a9b-162">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="33a9b-162">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="33a9b-163">快速入门：将应用程序注册到 Microsoft 标识平台</span><span class="sxs-lookup"><span data-stu-id="33a9b-163">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="33a9b-164">快速入门：配置应用程序以公开 Web API</span><span class="sxs-lookup"><span data-stu-id="33a9b-164">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
