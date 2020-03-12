---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 76bcac29d86a236e56c0eaea24a694c4845ecbcf
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083583"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="fb14a-102">使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="fb14a-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="fb14a-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fb14a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="fb14a-104">创建使用[Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的 Blazor WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="fb14a-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="fb14a-105">[创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="fb14a-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="fb14a-106">在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="fb14a-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fb14a-107">提供应用的**名称**（例如， **Blazor 客户端 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="fb14a-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="fb14a-108">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="fb14a-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="fb14a-109">你**只能在此组织目录中选择帐户**。</span><span class="sxs-lookup"><span data-stu-id="fb14a-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="fb14a-110">将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="fb14a-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="fb14a-111">禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。</span><span class="sxs-lookup"><span data-stu-id="fb14a-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="fb14a-112">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="fb14a-112">Select **Register**.</span></span>

<span data-ttu-id="fb14a-113">在 "**身份验证**" > **平台配置** > **Web**：</span><span class="sxs-lookup"><span data-stu-id="fb14a-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="fb14a-114">确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="fb14a-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="fb14a-115">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="fb14a-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="fb14a-116">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="fb14a-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="fb14a-117">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="fb14a-117">Select the **Save** button.</span></span>

<span data-ttu-id="fb14a-118">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="fb14a-118">Record the following information:</span></span>

* <span data-ttu-id="fb14a-119">应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="fb14a-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="fb14a-120">目录 ID （租户 ID）（例如 `22222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="fb14a-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="fb14a-121">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="fb14a-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="fb14a-122">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="fb14a-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fb14a-123">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="fb14a-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fb14a-124">身份验证包</span><span class="sxs-lookup"><span data-stu-id="fb14a-124">Authentication package</span></span>

<span data-ttu-id="fb14a-125">创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="fb14a-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fb14a-126">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="fb14a-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fb14a-127">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="fb14a-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fb14a-128">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="fb14a-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fb14a-129">`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。</span><span class="sxs-lookup"><span data-stu-id="fb14a-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fb14a-130">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="fb14a-130">Authentication service support</span></span>

<span data-ttu-id="fb14a-131">使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="fb14a-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fb14a-132">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="fb14a-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fb14a-133">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="fb14a-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="fb14a-134">`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="fb14a-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fb14a-135">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="fb14a-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="fb14a-136">Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="fb14a-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fb14a-137">若要将令牌预配为登录流的一部分，请将该作用域添加到 `MsalProviderOptions`的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="fb14a-137">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="fb14a-138">默认访问令牌范围必须是 `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` 格式（例如，`11111111-1111-1111-1111-111111111111/API.Access`）。</span><span class="sxs-lookup"><span data-stu-id="fb14a-138">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="fb14a-139">如果向范围设置提供方案或方案和主机（如 Azure 门户中所示），则当*客户端应用*收到来自*服务器 API 应用*的*401 未经授权*响应时，将引发未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="fb14a-139">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

## <a name="index-page"></a><span data-ttu-id="fb14a-140">索引页</span><span class="sxs-lookup"><span data-stu-id="fb14a-140">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="fb14a-141">应用组件</span><span class="sxs-lookup"><span data-stu-id="fb14a-141">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fb14a-142">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="fb14a-142">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fb14a-143">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="fb14a-143">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fb14a-144">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="fb14a-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fb14a-145">其他资源</span><span class="sxs-lookup"><span data-stu-id="fb14a-145">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
