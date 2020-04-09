---
title: 使用身份验证库保护BlazorASP.NET核心 Web 大会独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977036"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="78722-102">使用身份验证库保护BlazorASP.NET核心 Web 大会独立应用</span><span class="sxs-lookup"><span data-stu-id="78722-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="78722-103">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="78722-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="78722-104">*对于 Azure 活动目录 （AAD） 和 Azure 活动目录 B2C （AAD B2C），不要遵循本主题中的指南。请参阅此目录节点中的 AAD 和 AAD B2C 主题。*</span><span class="sxs-lookup"><span data-stu-id="78722-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="78722-105">要创建Blazor使用`Microsoft.AspNetCore.Components.WebAssembly.Authentication`库的 WebAssembly 独立应用，请在命令外壳中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78722-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="78722-106">要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample`</span><span class="sxs-lookup"><span data-stu-id="78722-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="78722-107">文件夹名称也将成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="78722-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="78722-108">在可视化工作室[中，创建BlazorWeb 组装应用](xref:blazor/get-started)。</span><span class="sxs-lookup"><span data-stu-id="78722-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="78722-109">使用**应用商店用户帐户应用内**选项将**身份验证**设置为**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="78722-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="78722-110">身份验证包</span><span class="sxs-lookup"><span data-stu-id="78722-110">Authentication package</span></span>

<span data-ttu-id="78722-111">创建应用以使用个人用户帐户时，应用会自动在应用的项目文件中接收`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包的包引用。</span><span class="sxs-lookup"><span data-stu-id="78722-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="78722-112">该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="78722-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="78722-113">如果向应用添加身份验证，则手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="78722-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="78722-114">在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。</span><span class="sxs-lookup"><span data-stu-id="78722-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="78722-115">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="78722-115">Authentication service support</span></span>

<span data-ttu-id="78722-116">使用`AddOidcAuthentication``Microsoft.AspNetCore.Components.WebAssembly.Authentication`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="78722-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="78722-117">此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="78722-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="78722-118">*Program.cs*：</span><span class="sxs-lookup"><span data-stu-id="78722-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="78722-119">使用开放 ID 连接 （OIDC） 为独立应用提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="78722-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="78722-120">该方法`AddOidcAuthentication`接受回调以配置使用 OIDC 对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="78722-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="78722-121">配置应用所需的值可以从符合 OIDC 的 IP 获得。</span><span class="sxs-lookup"><span data-stu-id="78722-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="78722-122">注册应用时获取值，这些值通常发生在其联机门户中。</span><span class="sxs-lookup"><span data-stu-id="78722-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="78722-123">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="78722-123">Access token scopes</span></span>

<span data-ttu-id="78722-124">WebAssemblyBlazor模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="78722-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="78722-125">要将令牌预配为登录流的一部分，请将作用域添加到 的`OidcProviderOptions`默认令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="78722-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="78722-126">如果 Azure 门户提供作用域 URI，并且应用在收到来自 API 的*401 未授权*响应时**引发未处理的异常**，请尝试使用不包括方案和主机的范围 URI。</span><span class="sxs-lookup"><span data-stu-id="78722-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="78722-127">例如，Azure 门户可以提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="78722-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="78722-128">提供无方案和主机的范围 URI：</span><span class="sxs-lookup"><span data-stu-id="78722-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="78722-129">有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。</span><span class="sxs-lookup"><span data-stu-id="78722-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="78722-130">导入文件</span><span class="sxs-lookup"><span data-stu-id="78722-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="78722-131">索引页面</span><span class="sxs-lookup"><span data-stu-id="78722-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="78722-132">应用组件</span><span class="sxs-lookup"><span data-stu-id="78722-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="78722-133">重定向到登录组件</span><span class="sxs-lookup"><span data-stu-id="78722-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="78722-134">登录显示组件</span><span class="sxs-lookup"><span data-stu-id="78722-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="78722-135">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="78722-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="78722-136">其他资源</span><span class="sxs-lookup"><span data-stu-id="78722-136">Additional resources</span></span>

* [<span data-ttu-id="78722-137">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="78722-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
