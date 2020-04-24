---
title: 使用 Azure Active Directory 保护Blazor ASP.NET Core WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 71229f41f3f1021aa9ad02402af21de51d7eeee4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111196"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="f93c2-102">使用 Azure Active Directory 保护Blazor ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="f93c2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="f93c2-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f93c2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="f93c2-104">本文中的指南适用于 ASP.NET Core 3.2 预览版4。</span><span class="sxs-lookup"><span data-stu-id="f93c2-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="f93c2-105">本主题将更新到2005年4月24日星期五的预览版5。</span><span class="sxs-lookup"><span data-stu-id="f93c2-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="f93c2-106">创建使用Blazor [Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的 WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="f93c2-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="f93c2-107">[创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="f93c2-107">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="f93c2-108">在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="f93c2-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="f93c2-109">提供应用的**名称**（例如， \*\* Blazor客户端 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="f93c2-109">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="f93c2-110">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="f93c2-110">Choose a **Supported account types**.</span></span> <span data-ttu-id="f93c2-111">你**只能在此组织目录中选择帐户**。</span><span class="sxs-lookup"><span data-stu-id="f93c2-111">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="f93c2-112">将 "**重定向 uri** " 下拉状态设置为 " **Web**"，并提供`https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="f93c2-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="f93c2-113">禁用 "**将** > **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="f93c2-113">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f93c2-114">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="f93c2-114">Select **Register**.</span></span>

<span data-ttu-id="f93c2-115">在 "**身份验证** > **平台配置** > "**Web**：</span><span class="sxs-lookup"><span data-stu-id="f93c2-115">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="f93c2-116">确认存在的**重定向 URI** `https://localhost:5001/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="f93c2-116">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f93c2-117">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="f93c2-117">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="f93c2-118">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="f93c2-118">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f93c2-119">选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="f93c2-119">Select the **Save** button.</span></span>

<span data-ttu-id="f93c2-120">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="f93c2-120">Record the following information:</span></span>

* <span data-ttu-id="f93c2-121">应用程序 ID （客户端 ID）（例如`11111111-1111-1111-1111-111111111111`，）</span><span class="sxs-lookup"><span data-stu-id="f93c2-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="f93c2-122">目录 ID （租户 ID）（例如， `22222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="f93c2-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="f93c2-123">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="f93c2-123">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="f93c2-124">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。</span><span class="sxs-lookup"><span data-stu-id="f93c2-124">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f93c2-125">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="f93c2-125">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="f93c2-126">身份验证包</span><span class="sxs-lookup"><span data-stu-id="f93c2-126">Authentication package</span></span>

<span data-ttu-id="f93c2-127">创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="f93c2-127">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f93c2-128">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="f93c2-128">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f93c2-129">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="f93c2-129">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="f93c2-130">将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。</span><span class="sxs-lookup"><span data-stu-id="f93c2-130">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="f93c2-131">`Microsoft.Authentication.WebAssembly.Msal`包可传递将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="f93c2-131">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f93c2-132">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="f93c2-132">Authentication service support</span></span>

<span data-ttu-id="f93c2-133">使用`AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="f93c2-133">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f93c2-134">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="f93c2-134">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f93c2-135">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="f93c2-135">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="f93c2-136">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="f93c2-136">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f93c2-137">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="f93c2-137">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="f93c2-138">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="f93c2-138">Access token scopes</span></span>

<span data-ttu-id="f93c2-139">Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f93c2-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f93c2-140">若要将令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌范围中`MsalProviderOptions`：</span><span class="sxs-lookup"><span data-stu-id="f93c2-140">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="f93c2-141">如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="f93c2-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="f93c2-142">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="f93c2-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="f93c2-143">提供不含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="f93c2-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="f93c2-144">有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。</span><span class="sxs-lookup"><span data-stu-id="f93c2-144">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="f93c2-145">导入文件</span><span class="sxs-lookup"><span data-stu-id="f93c2-145">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f93c2-146">索引页面</span><span class="sxs-lookup"><span data-stu-id="f93c2-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="f93c2-147">应用组件</span><span class="sxs-lookup"><span data-stu-id="f93c2-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f93c2-148">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="f93c2-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f93c2-149">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="f93c2-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="f93c2-150">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="f93c2-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f93c2-151">其他资源</span><span class="sxs-lookup"><span data-stu-id="f93c2-151">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="f93c2-152">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="f93c2-152">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
