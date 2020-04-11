---
title: 使用 AzureBlazor活动目录 B2C 保护 ASP.NET核心 Web 组件独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123457"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="1398d-102">使用 AzureBlazor活动目录 B2C 保护 ASP.NET核心 Web 组件独立应用</span><span class="sxs-lookup"><span data-stu-id="1398d-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="1398d-103">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1398d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1398d-104">要创建Blazor使用[Azure 活动目录 （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 Web 大会独立应用，请执行：</span><span class="sxs-lookup"><span data-stu-id="1398d-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="1398d-105">按照以下主题中的指南创建租户并在 Azure 门户中注册 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="1398d-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="1398d-106">[创建 AAD B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)&ndash;记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="1398d-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="1398d-107">1\.</span><span class="sxs-lookup"><span data-stu-id="1398d-107">1\.</span></span> <span data-ttu-id="1398d-108">AAD B2C 实例（例如`https://contoso.b2clogin.com/`，包括尾随斜杠）</span><span class="sxs-lookup"><span data-stu-id="1398d-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="1398d-109">2\.</span><span class="sxs-lookup"><span data-stu-id="1398d-109">2\.</span></span> <span data-ttu-id="1398d-110">AAD B2C 租户域（例如`contoso.onmicrosoft.com`，</span><span class="sxs-lookup"><span data-stu-id="1398d-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="1398d-111">[注册 Web 应用程序](/azure/active-directory-b2c/tutorial-register-applications)&ndash;在应用注册期间进行以下选择：</span><span class="sxs-lookup"><span data-stu-id="1398d-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="1398d-112">1\.</span><span class="sxs-lookup"><span data-stu-id="1398d-112">1\.</span></span> <span data-ttu-id="1398d-113">将**Web 应用/Web API**设置为 **"是**"。</span><span class="sxs-lookup"><span data-stu-id="1398d-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="1398d-114">2\.</span><span class="sxs-lookup"><span data-stu-id="1398d-114">2\.</span></span> <span data-ttu-id="1398d-115">将**允许隐式流**设置为 **"是**"。</span><span class="sxs-lookup"><span data-stu-id="1398d-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="1398d-116">3\.</span><span class="sxs-lookup"><span data-stu-id="1398d-116">3\.</span></span> <span data-ttu-id="1398d-117">添加 的`https://localhost:5001/authentication/login-callback`**回复 URL。**</span><span class="sxs-lookup"><span data-stu-id="1398d-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="1398d-118">记录应用程序 ID（客户端 ID）（例如， `11111111-1111-1111-1111-111111111111`</span><span class="sxs-lookup"><span data-stu-id="1398d-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="1398d-119">[创建用户流](/azure/active-directory-b2c/tutorial-create-user-flows)&ndash;创建注册和登录用户流。</span><span class="sxs-lookup"><span data-stu-id="1398d-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="1398d-120">至少，选择**应用程序声明** > **显示名称**用户属性以填充`context.User.Identity.Name``LoginDisplay`组件中的 （*共享/LoginDisplay.razor）。*</span><span class="sxs-lookup"><span data-stu-id="1398d-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="1398d-121">记录为应用创建的注册和登录用户流名称（例如。 `B2C_1_signupsignin`</span><span class="sxs-lookup"><span data-stu-id="1398d-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="1398d-122">将以下命令中的占位符替换为前面记录的信息，并在命令 shell 中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="1398d-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="1398d-123">要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample`</span><span class="sxs-lookup"><span data-stu-id="1398d-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1398d-124">文件夹名称也将成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="1398d-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1398d-125">身份验证包</span><span class="sxs-lookup"><span data-stu-id="1398d-125">Authentication package</span></span>

<span data-ttu-id="1398d-126">当创建应用以使用单个 B2C 帐户 （）`IndividualB2C`时，应用会自动接收 Microsoft[身份验证库](/azure/active-directory/develop/msal-overview)（）`Microsoft.Authentication.WebAssembly.Msal`的包引用 。</span><span class="sxs-lookup"><span data-stu-id="1398d-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1398d-127">该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="1398d-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1398d-128">如果向应用添加身份验证，则手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="1398d-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1398d-129">在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。</span><span class="sxs-lookup"><span data-stu-id="1398d-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1398d-130">包`Microsoft.Authentication.WebAssembly.Msal`会临时将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="1398d-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1398d-131">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="1398d-131">Authentication service support</span></span>

<span data-ttu-id="1398d-132">使用`AddMsalAuthentication``Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="1398d-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1398d-133">此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="1398d-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1398d-134">*Program.cs*：</span><span class="sxs-lookup"><span data-stu-id="1398d-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="1398d-135">该方法`AddMsalAuthentication`接受回调以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="1398d-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1398d-136">注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="1398d-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="1398d-137">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="1398d-137">Access token scopes</span></span>

<span data-ttu-id="1398d-138">WebAssemblyBlazor模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="1398d-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1398d-139">要将令牌预配为登录流的一部分，请将作用域添加到 的`MsalProviderOptions`默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="1398d-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1398d-140">如果 Azure 门户提供作用域 URI，并且应用在收到来自 API 的*401 未授权*响应时**引发未处理的异常**，请尝试使用不包括方案和主机的范围 URI。</span><span class="sxs-lookup"><span data-stu-id="1398d-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1398d-141">例如，Azure 门户可以提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="1398d-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1398d-142">提供无方案和主机的范围 URI：</span><span class="sxs-lookup"><span data-stu-id="1398d-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1398d-143">有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。</span><span class="sxs-lookup"><span data-stu-id="1398d-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="1398d-144">导入文件</span><span class="sxs-lookup"><span data-stu-id="1398d-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1398d-145">索引页面</span><span class="sxs-lookup"><span data-stu-id="1398d-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="1398d-146">应用组件</span><span class="sxs-lookup"><span data-stu-id="1398d-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1398d-147">重定向到登录组件</span><span class="sxs-lookup"><span data-stu-id="1398d-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1398d-148">登录显示组件</span><span class="sxs-lookup"><span data-stu-id="1398d-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1398d-149">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="1398d-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1398d-150">其他资源</span><span class="sxs-lookup"><span data-stu-id="1398d-150">Additional resources</span></span>

* [<span data-ttu-id="1398d-151">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="1398d-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="1398d-152">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="1398d-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="1398d-153">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="1398d-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
