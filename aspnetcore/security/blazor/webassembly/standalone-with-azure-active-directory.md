---
title: 使用 AzureBlazor活动目录保护ASP.NET核心 Web 大会独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976986"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="588aa-102">使用 AzureBlazor活动目录保护ASP.NET核心 Web 大会独立应用</span><span class="sxs-lookup"><span data-stu-id="588aa-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="588aa-103">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="588aa-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="588aa-104">要创建Blazor使用[Azure 活动目录 （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的 Web 组装独立应用，请执行：</span><span class="sxs-lookup"><span data-stu-id="588aa-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="588aa-105">[创建 AAD 租户和 Web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="588aa-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="588aa-106">在 Azure 门户的**Azure 活动目录** > **应用注册区域注册**AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="588aa-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="588aa-107">为应用提供**名称**（例如，**Blazor客户端 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="588aa-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="588aa-108">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="588aa-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="588aa-109">您只能为此体验选择**此组织目录中的帐户**。</span><span class="sxs-lookup"><span data-stu-id="588aa-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="588aa-110">将**重定向 URI**下拉列表设置为**Web，** 并提供 重定向 URI。 `https://localhost:5001/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="588aa-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="588aa-111">禁用 **"权限** > **授予管理员集中打开"和offline_access权限**复选框。</span><span class="sxs-lookup"><span data-stu-id="588aa-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="588aa-112">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="588aa-112">Select **Register**.</span></span>

<span data-ttu-id="588aa-113">在**身份验证** > **平台配置中** > **，Web**：</span><span class="sxs-lookup"><span data-stu-id="588aa-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="588aa-114">确认存在 重定向`https://localhost:5001/authentication/login-callback` **URI。**</span><span class="sxs-lookup"><span data-stu-id="588aa-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="588aa-115">对于**隐式授予**，选择 Access**令牌**和**ID 令牌的**复选框。</span><span class="sxs-lookup"><span data-stu-id="588aa-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="588aa-116">此体验可以接受应用的剩余默认值。</span><span class="sxs-lookup"><span data-stu-id="588aa-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="588aa-117">选择"**保存**"按钮。</span><span class="sxs-lookup"><span data-stu-id="588aa-117">Select the **Save** button.</span></span>

<span data-ttu-id="588aa-118">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="588aa-118">Record the following information:</span></span>

* <span data-ttu-id="588aa-119">应用程序 ID（客户端 ID）（例如`11111111-1111-1111-1111-111111111111`）</span><span class="sxs-lookup"><span data-stu-id="588aa-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="588aa-120">目录 ID（租户 ID）（例如`22222222-2222-2222-2222-222222222222`）</span><span class="sxs-lookup"><span data-stu-id="588aa-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="588aa-121">将以下命令中的占位符替换为前面记录的信息，并在命令 shell 中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="588aa-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="588aa-122">要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample`</span><span class="sxs-lookup"><span data-stu-id="588aa-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="588aa-123">文件夹名称也将成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="588aa-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="588aa-124">身份验证包</span><span class="sxs-lookup"><span data-stu-id="588aa-124">Authentication package</span></span>

<span data-ttu-id="588aa-125">当创建应用以使用工作帐户或学校帐户 （）`SingleOrg`时，应用会自动接收 Microsoft[身份验证库](/azure/active-directory/develop/msal-overview)（）`Microsoft.Authentication.WebAssembly.Msal`的包引用 。</span><span class="sxs-lookup"><span data-stu-id="588aa-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="588aa-126">该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="588aa-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="588aa-127">如果向应用添加身份验证，则手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="588aa-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="588aa-128">在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。</span><span class="sxs-lookup"><span data-stu-id="588aa-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="588aa-129">包`Microsoft.Authentication.WebAssembly.Msal`会临时将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="588aa-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="588aa-130">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="588aa-130">Authentication service support</span></span>

<span data-ttu-id="588aa-131">使用`AddMsalAuthentication``Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="588aa-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="588aa-132">此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="588aa-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="588aa-133">*Program.cs*：</span><span class="sxs-lookup"><span data-stu-id="588aa-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="588aa-134">该方法`AddMsalAuthentication`接受回调以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="588aa-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="588aa-135">注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="588aa-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="588aa-136">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="588aa-136">Access token scopes</span></span>

<span data-ttu-id="588aa-137">WebAssemblyBlazor模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="588aa-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="588aa-138">要将令牌预配为登录流的一部分，请将作用域添加到 的`MsalProviderOptions`默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="588aa-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="588aa-139">如果 Azure 门户提供作用域 URI，并且应用在收到来自 API 的*401 未授权*响应时**引发未处理的异常**，请尝试使用不包括方案和主机的范围 URI。</span><span class="sxs-lookup"><span data-stu-id="588aa-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="588aa-140">例如，Azure 门户可以提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="588aa-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="588aa-141">提供无方案和主机的范围 URI：</span><span class="sxs-lookup"><span data-stu-id="588aa-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="588aa-142">有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。</span><span class="sxs-lookup"><span data-stu-id="588aa-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="588aa-143">导入文件</span><span class="sxs-lookup"><span data-stu-id="588aa-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="588aa-144">索引页面</span><span class="sxs-lookup"><span data-stu-id="588aa-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="588aa-145">应用组件</span><span class="sxs-lookup"><span data-stu-id="588aa-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="588aa-146">重定向到登录组件</span><span class="sxs-lookup"><span data-stu-id="588aa-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="588aa-147">登录显示组件</span><span class="sxs-lookup"><span data-stu-id="588aa-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="588aa-148">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="588aa-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="588aa-149">其他资源</span><span class="sxs-lookup"><span data-stu-id="588aa-149">Additional resources</span></span>

* [<span data-ttu-id="588aa-150">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="588aa-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="588aa-151">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="588aa-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
