---
title: 使用 Microsoft 帐户Blazor保护 ASP.NET Core WebAssembly 独立应用程序
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6f884cbfc9ac50f38c415af522d3d09a3ef38712
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976839"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="25114-102">使用 Microsoft 帐户Blazor保护 ASP.NET Core WebAssembly 独立应用程序</span><span class="sxs-lookup"><span data-stu-id="25114-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="25114-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25114-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="25114-104">若要创建Blazor将 Microsoft 帐户用于身份验证[Azure Active Directory （AAD）](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)的 WebAssembly 独立应用程序，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="25114-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="25114-105">创建 AAD 租户和 web 应用程序</span><span class="sxs-lookup"><span data-stu-id="25114-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="25114-106">在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="25114-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="25114-107">1\.</span><span class="sxs-lookup"><span data-stu-id="25114-107">1\.</span></span> <span data-ttu-id="25114-108">提供应用的**名称**（例如， \*\* Blazor客户端 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="25114-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="25114-109">2\.</span><span class="sxs-lookup"><span data-stu-id="25114-109">2\.</span></span> <span data-ttu-id="25114-110">在 "**支持的帐户类型**" 中，选择**任何组织目录中的帐户**。</span><span class="sxs-lookup"><span data-stu-id="25114-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="25114-111">3。</span><span class="sxs-lookup"><span data-stu-id="25114-111">3\.</span></span> <span data-ttu-id="25114-112">将 "**重定向 uri** " 下拉状态设置为 " **Web**"，并提供`https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="25114-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="25114-113">4 \。</span><span class="sxs-lookup"><span data-stu-id="25114-113">4\.</span></span> <span data-ttu-id="25114-114">禁用 "**将** > **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="25114-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="25114-115">5 \。</span><span class="sxs-lookup"><span data-stu-id="25114-115">5\.</span></span> <span data-ttu-id="25114-116">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="25114-116">Select **Register**.</span></span>

   <span data-ttu-id="25114-117">在 "**身份验证** > **平台配置** > "**Web**：</span><span class="sxs-lookup"><span data-stu-id="25114-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="25114-118">1\.</span><span class="sxs-lookup"><span data-stu-id="25114-118">1\.</span></span> <span data-ttu-id="25114-119">确认存在的**重定向 URI** `https://localhost:5001/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="25114-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="25114-120">2\.</span><span class="sxs-lookup"><span data-stu-id="25114-120">2\.</span></span> <span data-ttu-id="25114-121">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="25114-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="25114-122">3。</span><span class="sxs-lookup"><span data-stu-id="25114-122">3\.</span></span> <span data-ttu-id="25114-123">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="25114-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="25114-124">4 \。</span><span class="sxs-lookup"><span data-stu-id="25114-124">4\.</span></span> <span data-ttu-id="25114-125">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="25114-125">Select the **Save** button.</span></span>

   <span data-ttu-id="25114-126">记录应用程序 ID （客户端 ID）（例如`11111111-1111-1111-1111-111111111111`）。</span><span class="sxs-lookup"><span data-stu-id="25114-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="25114-127">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="25114-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="25114-128">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。</span><span class="sxs-lookup"><span data-stu-id="25114-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="25114-129">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="25114-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="25114-130">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="25114-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="25114-131">使用 Microsoft 帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="25114-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="25114-132">如果已正确配置应用，请使用与独立Blazor应用相同的方法为 Microsoft api 请求访问令牌。</span><span class="sxs-lookup"><span data-stu-id="25114-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="25114-133">有关详细信息，请参阅[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="25114-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="25114-134">身份验证包</span><span class="sxs-lookup"><span data-stu-id="25114-134">Authentication package</span></span>

<span data-ttu-id="25114-135">创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="25114-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="25114-136">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="25114-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="25114-137">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="25114-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="25114-138">将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。</span><span class="sxs-lookup"><span data-stu-id="25114-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="25114-139">`Microsoft.Authentication.WebAssembly.Msal`包可传递将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="25114-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="25114-140">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="25114-140">Authentication service support</span></span>

<span data-ttu-id="25114-141">使用`AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="25114-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="25114-142">此方法设置应用与Identity提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="25114-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="25114-143">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="25114-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="25114-144">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="25114-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="25114-145">注册应用时，可以从 Microsoft 帐户配置获取配置该应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="25114-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="25114-146">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="25114-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="25114-147">示例：</span><span class="sxs-lookup"><span data-stu-id="25114-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="25114-148">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="25114-148">Access token scopes</span></span>

<span data-ttu-id="25114-149">Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="25114-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="25114-150">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中`MsalProviderOptions`：</span><span class="sxs-lookup"><span data-stu-id="25114-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="25114-151">如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="25114-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="25114-152">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="25114-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="25114-153">提供不含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="25114-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="25114-154">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="25114-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="25114-155">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="25114-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="25114-156">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="25114-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="25114-157">导入文件</span><span class="sxs-lookup"><span data-stu-id="25114-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="25114-158">索引页面</span><span class="sxs-lookup"><span data-stu-id="25114-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="25114-159">应用组件</span><span class="sxs-lookup"><span data-stu-id="25114-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="25114-160">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="25114-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="25114-161">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="25114-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="25114-162">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="25114-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="25114-163">其他资源</span><span class="sxs-lookup"><span data-stu-id="25114-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="25114-164">快速入门：将应用程序注册到 Microsoft 标识平台</span><span class="sxs-lookup"><span data-stu-id="25114-164">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="25114-165">快速入门：配置应用程序以公开 Web API</span><span class="sxs-lookup"><span data-stu-id="25114-165">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
