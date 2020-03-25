---
title: 使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: bb03ef1e6d216cfc06e2b91919c64f92f2ef634e
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219267"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="2bb99-102">使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="2bb99-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="2bb99-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2bb99-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="2bb99-104">创建使用[Azure Active Directory （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 Blazor WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="2bb99-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="2bb99-105">按照以下主题中的指导在 Azure 门户中创建租户并注册 web 应用：</span><span class="sxs-lookup"><span data-stu-id="2bb99-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="2bb99-106">[创建 AAD B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)&ndash; 记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="2bb99-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="2bb99-107">1\.</span><span class="sxs-lookup"><span data-stu-id="2bb99-107">1\.</span></span> <span data-ttu-id="2bb99-108">AAD B2C 实例（例如，`https://contoso.b2clogin.com/`，其中包含尾随斜杠）</span><span class="sxs-lookup"><span data-stu-id="2bb99-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="2bb99-109">2\.</span><span class="sxs-lookup"><span data-stu-id="2bb99-109">2\.</span></span> <span data-ttu-id="2bb99-110">AAD B2C 租户域（例如，`contoso.onmicrosoft.com`）</span><span class="sxs-lookup"><span data-stu-id="2bb99-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="2bb99-111">[注册 web 应用程序](/azure/active-directory-b2c/tutorial-register-applications)&ndash; 在应用注册过程中进行以下选择：</span><span class="sxs-lookup"><span data-stu-id="2bb99-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="2bb99-112">1\.</span><span class="sxs-lookup"><span data-stu-id="2bb99-112">1\.</span></span> <span data-ttu-id="2bb99-113">将**Web 应用/WEB API**设置为 **"是"** 。</span><span class="sxs-lookup"><span data-stu-id="2bb99-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="2bb99-114">2\.</span><span class="sxs-lookup"><span data-stu-id="2bb99-114">2\.</span></span> <span data-ttu-id="2bb99-115">将 "**允许隐式流**" 设置为 **"是"** 。</span><span class="sxs-lookup"><span data-stu-id="2bb99-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="2bb99-116">3\.</span><span class="sxs-lookup"><span data-stu-id="2bb99-116">3\.</span></span> <span data-ttu-id="2bb99-117">添加 `https://localhost:5001/authentication/login-callback`的**回复 URL** 。</span><span class="sxs-lookup"><span data-stu-id="2bb99-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="2bb99-118">记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）。</span><span class="sxs-lookup"><span data-stu-id="2bb99-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="2bb99-119">[创建用户流](/azure/active-directory-b2c/tutorial-create-user-flows)&ndash; 创建注册和登录用户流。</span><span class="sxs-lookup"><span data-stu-id="2bb99-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="2bb99-120">至少，选择 "**应用程序声明**" > "**显示名称**用户属性"，以填充 `LoginDisplay` 组件中的 `context.User.Identity.Name` （*Shared/LoginDisplay*）。</span><span class="sxs-lookup"><span data-stu-id="2bb99-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="2bb99-121">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin`）。</span><span class="sxs-lookup"><span data-stu-id="2bb99-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="2bb99-122">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="2bb99-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="2bb99-123">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="2bb99-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="2bb99-124">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="2bb99-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="2bb99-125">身份验证包</span><span class="sxs-lookup"><span data-stu-id="2bb99-125">Authentication package</span></span>

<span data-ttu-id="2bb99-126">创建应用以使用单个 B2C 帐户（`IndividualB2C`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="2bb99-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="2bb99-127">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="2bb99-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2bb99-128">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="2bb99-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="2bb99-129">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="2bb99-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="2bb99-130">`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。</span><span class="sxs-lookup"><span data-stu-id="2bb99-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="2bb99-131">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="2bb99-131">Authentication service support</span></span>

<span data-ttu-id="2bb99-132">使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="2bb99-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="2bb99-133">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="2bb99-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2bb99-134">Program.cs：</span><span class="sxs-lookup"><span data-stu-id="2bb99-134">*Program.cs*:</span></span>

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

<span data-ttu-id="2bb99-135">`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="2bb99-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2bb99-136">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="2bb99-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="2bb99-137">Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="2bb99-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="2bb99-138">若要将令牌预配为登录流的一部分，请将该作用域添加到 `MsalProviderOptions`的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="2bb99-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a><span data-ttu-id="2bb99-139">索引页面</span><span class="sxs-lookup"><span data-stu-id="2bb99-139">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="2bb99-140">应用组件</span><span class="sxs-lookup"><span data-stu-id="2bb99-140">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="2bb99-141">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="2bb99-141">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="2bb99-142">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="2bb99-142">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="2bb99-143">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="2bb99-143">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2bb99-144">其他资源</span><span class="sxs-lookup"><span data-stu-id="2bb99-144">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="2bb99-145">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="2bb99-145">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
