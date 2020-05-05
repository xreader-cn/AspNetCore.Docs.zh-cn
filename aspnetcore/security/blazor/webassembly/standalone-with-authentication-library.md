---
title: 使用身份验证Blazor库保护 ASP.NET Core WebAssembly 独立应用
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
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 6907a1213a6a9089e2aed885093c2fd38f972ad0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768047"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="ad7cc-102">使用身份验证Blazor库保护 ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="ad7cc-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="ad7cc-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ad7cc-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="ad7cc-104">*对于 Azure Active Directory （AAD）和 Azure Active Directory B2C （AAD B2C），请勿按照本主题中的指导进行操作。请参阅此目录节点中的 AAD 和 AAD B2C 主题。*</span><span class="sxs-lookup"><span data-stu-id="ad7cc-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="ad7cc-105">若要创建Blazor使用`Microsoft.AspNetCore.Components.WebAssembly.Authentication`库的 WebAssembly 独立应用程序，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ad7cc-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="ad7cc-106">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ad7cc-107">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="ad7cc-108">在 Visual Studio 中，[创建Blazor一个 WebAssembly 应用](xref:blazor/get-started)。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="ad7cc-109">将**身份验证**设置为具有 "**应用商店用户帐户应用内**" 选项的**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="ad7cc-110">身份验证包</span><span class="sxs-lookup"><span data-stu-id="ad7cc-110">Authentication package</span></span>

<span data-ttu-id="ad7cc-111">创建应用以使用单个用户帐户时，应用会在应用的项目文件中自动接收`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包的包引用。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="ad7cc-112">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ad7cc-113">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="ad7cc-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="ad7cc-114">将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="ad7cc-115">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="ad7cc-115">Authentication service support</span></span>

<span data-ttu-id="ad7cc-116">使用`AddOidcAuthentication` `Microsoft.AspNetCore.Components.WebAssembly.Authentication`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="ad7cc-117">此方法设置应用与Identity提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ad7cc-118">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="ad7cc-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="ad7cc-119">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="ad7cc-119">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="ad7cc-120">使用 Open ID Connect （OIDC）提供对独立应用程序的身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-120">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="ad7cc-121">`AddOidcAuthentication`方法接受回调来配置使用 OIDC 对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-121">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="ad7cc-122">配置应用所需的值可以从 OIDC 兼容的 IP 获得。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-122">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="ad7cc-123">注册应用时获取值，此操作通常发生在其联机门户中。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-123">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="ad7cc-124">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="ad7cc-124">Access token scopes</span></span>

<span data-ttu-id="ad7cc-125">Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-125">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="ad7cc-126">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认令牌范围中`OidcProviderOptions`：</span><span class="sxs-lookup"><span data-stu-id="ad7cc-126">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="ad7cc-127">如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="ad7cc-127">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="ad7cc-128">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="ad7cc-128">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="ad7cc-129">提供不含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="ad7cc-129">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="ad7cc-130">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="ad7cc-130">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="ad7cc-131">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="ad7cc-131">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="ad7cc-132">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="ad7cc-132">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="ad7cc-133">导入文件</span><span class="sxs-lookup"><span data-stu-id="ad7cc-133">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="ad7cc-134">索引页面</span><span class="sxs-lookup"><span data-stu-id="ad7cc-134">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="ad7cc-135">应用组件</span><span class="sxs-lookup"><span data-stu-id="ad7cc-135">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ad7cc-136">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="ad7cc-136">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ad7cc-137">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="ad7cc-137">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="ad7cc-138">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="ad7cc-138">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ad7cc-139">其他资源</span><span class="sxs-lookup"><span data-stu-id="ad7cc-139">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
