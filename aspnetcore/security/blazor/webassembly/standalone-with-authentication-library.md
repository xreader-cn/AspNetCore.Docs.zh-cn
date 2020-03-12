---
title: 使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: f9cc2884dcd94c729c45a056ae4327a2c75d34be
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083589"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="b9fa9-102">使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="b9fa9-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="b9fa9-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b9fa9-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="b9fa9-104">若要创建使用 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 库的 Blazor WebAssembly 独立应用程序，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9fa9-104">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="b9fa9-105">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-105">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b9fa9-106">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-106">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="b9fa9-107">在 Visual Studio 中，[创建 Blazor WebAssembly 应用](xref:blazor/get-started)。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-107">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="b9fa9-108">将**身份验证**设置为具有 "**应用商店用户帐户应用内**" 选项的**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-108">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b9fa9-109">身份验证包</span><span class="sxs-lookup"><span data-stu-id="b9fa9-109">Authentication package</span></span>

<span data-ttu-id="b9fa9-110">当创建应用以使用单个用户帐户时，应用会自动接收应用的项目文件中 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-110">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="b9fa9-111">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-111">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b9fa9-112">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="b9fa9-112">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="b9fa9-113">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-113">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b9fa9-114">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="b9fa9-114">Authentication service support</span></span>

<span data-ttu-id="b9fa9-115">使用 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包提供的 `AddOidcAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-115">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="b9fa9-116">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-116">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b9fa9-117">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="b9fa9-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="b9fa9-118">使用 Open ID Connect （OIDC）提供对独立应用程序的身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-118">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="b9fa9-119">`AddOidcAuthentication` 方法接受回调来配置使用 OIDC 对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-119">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="b9fa9-120">可以从 IP （如 Google、Microsoft 或其他与 OIDC 兼容的提供程序）获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-120">The values required for configuring the app can be obtained from the IP, such as Google, Microsoft, or other OIDC-compliant provider.</span></span> <span data-ttu-id="b9fa9-121">注册应用时获取值，此操作通常发生在其联机门户中。</span><span class="sxs-lookup"><span data-stu-id="b9fa9-121">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="index-page"></a><span data-ttu-id="b9fa9-122">索引页</span><span class="sxs-lookup"><span data-stu-id="b9fa9-122">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="b9fa9-123">应用组件</span><span class="sxs-lookup"><span data-stu-id="b9fa9-123">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b9fa9-124">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="b9fa9-124">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b9fa9-125">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="b9fa9-125">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b9fa9-126">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="b9fa9-126">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
