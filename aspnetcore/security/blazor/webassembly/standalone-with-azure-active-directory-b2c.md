---
title: 使用 Azure Active Directory B2C 保护Blazor ASP.NET Core WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0fb4f4176f214d6bf0c005838a0ccbe4487243f2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767969"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="bd1f1-102">使用 Azure Active Directory B2C 保护Blazor ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="bd1f1-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="bd1f1-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd1f1-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="bd1f1-104">创建使用Blazor [Azure Active Directory （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="bd1f1-105">按照以下主题中的指导在 Azure 门户中创建租户并注册 web 应用：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="bd1f1-106">[创建 AAD B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant) &ndash;记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="bd1f1-107">1\.</span><span class="sxs-lookup"><span data-stu-id="bd1f1-107">1\.</span></span> <span data-ttu-id="bd1f1-108">AAD B2C 实例（例如`https://contoso.b2clogin.com/`，包含尾随斜杠）</span><span class="sxs-lookup"><span data-stu-id="bd1f1-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="bd1f1-109">2\.</span><span class="sxs-lookup"><span data-stu-id="bd1f1-109">2\.</span></span> <span data-ttu-id="bd1f1-110">AAD B2C 租户域（例如`contoso.onmicrosoft.com`）</span><span class="sxs-lookup"><span data-stu-id="bd1f1-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="bd1f1-111">[注册 web 应用程序](/azure/active-directory-b2c/tutorial-register-applications) &ndash;在应用注册过程中进行以下选择：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="bd1f1-112">1\.</span><span class="sxs-lookup"><span data-stu-id="bd1f1-112">1\.</span></span> <span data-ttu-id="bd1f1-113">将**Web 应用/WEB API**设置为 **"是"**。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="bd1f1-114">2\.</span><span class="sxs-lookup"><span data-stu-id="bd1f1-114">2\.</span></span> <span data-ttu-id="bd1f1-115">将 "**允许隐式流**" 设置为 **"是"**。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="bd1f1-116">3。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-116">3\.</span></span> <span data-ttu-id="bd1f1-117">添加`https://localhost:5001/authentication/login-callback`的**回复 URL** 。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="bd1f1-118">记录应用程序 ID （客户端 ID）（例如`11111111-1111-1111-1111-111111111111`）。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="bd1f1-119">[创建用户流](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash;创建注册和登录用户流。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="bd1f1-120">至少，选择 "**应用程序声明** > **显示名称**用户" 属性以`context.User.Identity.Name`在`LoginDisplay`组件中填充（*Shared/LoginDisplay*）。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="bd1f1-121">记录为应用创建的注册和登录用户流名称（例如`B2C_1_signupsignin`）。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="bd1f1-122">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="bd1f1-123">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="bd1f1-124">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="bd1f1-125">身份验证包</span><span class="sxs-lookup"><span data-stu-id="bd1f1-125">Authentication package</span></span>

<span data-ttu-id="bd1f1-126">当创建应用以使用单个 B2C 帐户（`IndividualB2C`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="bd1f1-127">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bd1f1-128">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="bd1f1-129">将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="bd1f1-130">`Microsoft.Authentication.WebAssembly.Msal`包可传递将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="bd1f1-131">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="bd1f1-131">Authentication service support</span></span>

<span data-ttu-id="bd1f1-132">使用`AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="bd1f1-133">此方法设置应用与Identity提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="bd1f1-134">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="bd1f1-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="bd1f1-135">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="bd1f1-136">注册应用时，可以从 Microsoft 帐户配置获取配置该应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-136">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="bd1f1-137">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-137">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="bd1f1-138">示例：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-138">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="bd1f1-139">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="bd1f1-139">Access token scopes</span></span>

<span data-ttu-id="bd1f1-140">Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="bd1f1-141">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中`MsalProviderOptions`：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-141">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="bd1f1-142">如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="bd1f1-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="bd1f1-143">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="bd1f1-144">提供不含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="bd1f1-145">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="bd1f1-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="bd1f1-146">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="bd1f1-146">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="bd1f1-147">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="bd1f1-147">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="bd1f1-148">导入文件</span><span class="sxs-lookup"><span data-stu-id="bd1f1-148">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="bd1f1-149">索引页面</span><span class="sxs-lookup"><span data-stu-id="bd1f1-149">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="bd1f1-150">应用组件</span><span class="sxs-lookup"><span data-stu-id="bd1f1-150">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="bd1f1-151">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="bd1f1-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="bd1f1-152">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="bd1f1-152">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="bd1f1-153">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="bd1f1-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bd1f1-154">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd1f1-154">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="bd1f1-155">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="bd1f1-155">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="bd1f1-156">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="bd1f1-156">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
