---
title: Blazor使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 512fab439686e54b1d21576c7dad7b3cd320a8b1
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153599"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="3d50a-102">Blazor使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="3d50a-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="3d50a-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d50a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="3d50a-104">创建 Blazor 使用[AZURE ACTIVE DIRECTORY （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的 WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="3d50a-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="3d50a-105">[创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="3d50a-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="3d50a-106">在 Azure 门户的**Azure Active Directory**  >  **应用注册**区域中注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="3d50a-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3d50a-107">提供应用的**名称**（例如， \*\* Blazor 客户端 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="3d50a-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="3d50a-108">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="3d50a-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="3d50a-109">你**只能在此组织目录中选择帐户**。</span><span class="sxs-lookup"><span data-stu-id="3d50a-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="3d50a-110">将 "**重定向 uri** " 下拉状态设置为 " **Web**"，并提供的重定向 uri `https://localhost:5001/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="3d50a-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="3d50a-111">禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="3d50a-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3d50a-112">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="3d50a-112">Select **Register**.</span></span>

<span data-ttu-id="3d50a-113">在 "**身份验证**  >  **平台配置**"  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="3d50a-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3d50a-114">确认存在的**重定向 URI** `https://localhost:5001/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="3d50a-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3d50a-115">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="3d50a-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3d50a-116">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="3d50a-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3d50a-117">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="3d50a-117">Select the **Save** button.</span></span>

<span data-ttu-id="3d50a-118">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="3d50a-118">Record the following information:</span></span>

* <span data-ttu-id="3d50a-119">应用程序 ID （客户端 ID）（例如， `11111111-1111-1111-1111-111111111111` ）</span><span class="sxs-lookup"><span data-stu-id="3d50a-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="3d50a-120">目录 ID （租户 ID）（例如， `22222222-2222-2222-2222-222222222222` ）</span><span class="sxs-lookup"><span data-stu-id="3d50a-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="3d50a-121">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="3d50a-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="3d50a-122">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="3d50a-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3d50a-123">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="3d50a-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="3d50a-124">身份验证包</span><span class="sxs-lookup"><span data-stu-id="3d50a-124">Authentication package</span></span>

<span data-ttu-id="3d50a-125">创建应用以使用工作或学校帐户（ `SingleOrg` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（）的包引用 `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="3d50a-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3d50a-126">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="3d50a-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3d50a-127">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="3d50a-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="3d50a-128">`{VERSION}`将前面的包引用中的替换为 `Microsoft.AspNetCore.Blazor.Templates` 本文中所示的包版本 <xref:blazor/get-started> 。</span><span class="sxs-lookup"><span data-stu-id="3d50a-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="3d50a-129">`Microsoft.Authentication.WebAssembly.Msal`包可传递将 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="3d50a-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="3d50a-130">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="3d50a-130">Authentication service support</span></span>

<span data-ttu-id="3d50a-131">使用包提供的扩展方法在服务容器中注册对用户进行身份验证的支持 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="3d50a-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3d50a-132">此方法设置应用与 Identity 提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="3d50a-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3d50a-133">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="3d50a-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="3d50a-134">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="3d50a-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3d50a-135">注册应用时，可以从 Microsoft 帐户配置获取配置该应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="3d50a-135">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="3d50a-136">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="3d50a-136">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="3d50a-137">示例：</span><span class="sxs-lookup"><span data-stu-id="3d50a-137">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="3d50a-138">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="3d50a-138">Access token scopes</span></span>

<span data-ttu-id="3d50a-139">BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="3d50a-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="3d50a-140">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中 `MsalProviderOptions` ：</span><span class="sxs-lookup"><span data-stu-id="3d50a-140">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="3d50a-141">如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="3d50a-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="3d50a-142">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="3d50a-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="3d50a-143">提供不含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="3d50a-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="3d50a-144">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="3d50a-144">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="3d50a-145">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="3d50a-145">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="3d50a-146">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="3d50a-146">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="3d50a-147">导入文件</span><span class="sxs-lookup"><span data-stu-id="3d50a-147">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="3d50a-148">索引页面</span><span class="sxs-lookup"><span data-stu-id="3d50a-148">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="3d50a-149">应用组件</span><span class="sxs-lookup"><span data-stu-id="3d50a-149">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="3d50a-150">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="3d50a-150">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="3d50a-151">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="3d50a-151">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="3d50a-152">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="3d50a-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3d50a-153">其他资源</span><span class="sxs-lookup"><span data-stu-id="3d50a-153">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="3d50a-154">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="3d50a-154">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="3d50a-155">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="3d50a-155">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
