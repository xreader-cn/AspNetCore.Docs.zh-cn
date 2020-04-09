---
title: 使用 MicrosoftBlazor帐户保护ASP.NET核心 Web 大会独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977075"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="03f2b-102">使用 MicrosoftBlazor帐户保护ASP.NET核心 Web 大会独立应用</span><span class="sxs-lookup"><span data-stu-id="03f2b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="03f2b-103">哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="03f2b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="03f2b-104">要创建使用Blazor[具有 Azure 活动目录 （AAD）](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)的 Microsoft 帐户进行身份验证的 WebAssembly 独立应用，请执行：</span><span class="sxs-lookup"><span data-stu-id="03f2b-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="03f2b-105">创建 AAD 租户和 Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="03f2b-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="03f2b-106">在 Azure 门户的**Azure 活动目录** > **应用注册区域注册**AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="03f2b-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="03f2b-107">1\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-107">1\.</span></span> <span data-ttu-id="03f2b-108">为应用提供**名称**（例如，**Blazor客户端 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="03f2b-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="03f2b-109">2\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-109">2\.</span></span> <span data-ttu-id="03f2b-110">在 **"支持帐户类型**"**中，选择任何组织目录中的帐户**。</span><span class="sxs-lookup"><span data-stu-id="03f2b-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="03f2b-111">3\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-111">3\.</span></span> <span data-ttu-id="03f2b-112">将**重定向 URI**下拉列表设置为**Web，** 并提供 重定向 URI。 `https://localhost:5001/authentication/login-callback`</span><span class="sxs-lookup"><span data-stu-id="03f2b-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="03f2b-113">4\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-113">4\.</span></span> <span data-ttu-id="03f2b-114">禁用 **"权限** > **授予管理员集中打开"和offline_access权限**复选框。</span><span class="sxs-lookup"><span data-stu-id="03f2b-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="03f2b-115">5\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-115">5\.</span></span> <span data-ttu-id="03f2b-116">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="03f2b-116">Select **Register**.</span></span>

   <span data-ttu-id="03f2b-117">在**身份验证** > **平台配置中** > **，Web**：</span><span class="sxs-lookup"><span data-stu-id="03f2b-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="03f2b-118">1\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-118">1\.</span></span> <span data-ttu-id="03f2b-119">确认存在 重定向`https://localhost:5001/authentication/login-callback` **URI。**</span><span class="sxs-lookup"><span data-stu-id="03f2b-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="03f2b-120">2\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-120">2\.</span></span> <span data-ttu-id="03f2b-121">对于**隐式授予**，选择 Access**令牌**和**ID 令牌的**复选框。</span><span class="sxs-lookup"><span data-stu-id="03f2b-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="03f2b-122">3\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-122">3\.</span></span> <span data-ttu-id="03f2b-123">此体验可以接受应用的剩余默认值。</span><span class="sxs-lookup"><span data-stu-id="03f2b-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="03f2b-124">4\.</span><span class="sxs-lookup"><span data-stu-id="03f2b-124">4\.</span></span> <span data-ttu-id="03f2b-125">选择"**保存**"按钮。</span><span class="sxs-lookup"><span data-stu-id="03f2b-125">Select the **Save** button.</span></span>

   <span data-ttu-id="03f2b-126">记录应用程序 ID（客户端 ID）（例如， `11111111-1111-1111-1111-111111111111`</span><span class="sxs-lookup"><span data-stu-id="03f2b-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="03f2b-127">将以下命令中的占位符替换为前面记录的信息，并在命令 shell 中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="03f2b-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="03f2b-128">要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample`</span><span class="sxs-lookup"><span data-stu-id="03f2b-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="03f2b-129">文件夹名称也将成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="03f2b-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="03f2b-130">创建应用后，您应该能够：</span><span class="sxs-lookup"><span data-stu-id="03f2b-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="03f2b-131">使用 Microsoft 帐户登录应用。</span><span class="sxs-lookup"><span data-stu-id="03f2b-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="03f2b-132">使用与独立Blazor应用相同的方法请求 Microsoft API 的访问令牌，前提是您已正确配置应用。</span><span class="sxs-lookup"><span data-stu-id="03f2b-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="03f2b-133">有关详细信息，请参阅[快速入门：配置应用程序以公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="03f2b-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="03f2b-134">身份验证包</span><span class="sxs-lookup"><span data-stu-id="03f2b-134">Authentication package</span></span>

<span data-ttu-id="03f2b-135">当创建应用以使用工作帐户或学校帐户 （）`SingleOrg`时，应用会自动接收 Microsoft[身份验证库](/azure/active-directory/develop/msal-overview)（）`Microsoft.Authentication.WebAssembly.Msal`的包引用 。</span><span class="sxs-lookup"><span data-stu-id="03f2b-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="03f2b-136">该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="03f2b-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="03f2b-137">如果向应用添加身份验证，则手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="03f2b-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="03f2b-138">在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。</span><span class="sxs-lookup"><span data-stu-id="03f2b-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="03f2b-139">包`Microsoft.Authentication.WebAssembly.Msal`会临时将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="03f2b-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="03f2b-140">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="03f2b-140">Authentication service support</span></span>

<span data-ttu-id="03f2b-141">使用`AddMsalAuthentication``Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="03f2b-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="03f2b-142">此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="03f2b-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="03f2b-143">*Program.cs*：</span><span class="sxs-lookup"><span data-stu-id="03f2b-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="03f2b-144">该方法`AddMsalAuthentication`接受回调以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="03f2b-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="03f2b-145">注册应用时，可以从 Microsoft 帐户配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="03f2b-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="03f2b-146">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="03f2b-146">Access token scopes</span></span>

<span data-ttu-id="03f2b-147">WebAssemblyBlazor模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="03f2b-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="03f2b-148">要将令牌预配为登录流的一部分，请将作用域添加到 的`MsalProviderOptions`默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="03f2b-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="03f2b-149">如果 Azure 门户提供作用域 URI，并且应用在收到来自 API 的*401 未授权*响应时**引发未处理的异常**，请尝试使用不包括方案和主机的范围 URI。</span><span class="sxs-lookup"><span data-stu-id="03f2b-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="03f2b-150">例如，Azure 门户可以提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="03f2b-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="03f2b-151">提供无方案和主机的范围 URI：</span><span class="sxs-lookup"><span data-stu-id="03f2b-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="03f2b-152">有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。</span><span class="sxs-lookup"><span data-stu-id="03f2b-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="03f2b-153">导入文件</span><span class="sxs-lookup"><span data-stu-id="03f2b-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="03f2b-154">索引页面</span><span class="sxs-lookup"><span data-stu-id="03f2b-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="03f2b-155">应用组件</span><span class="sxs-lookup"><span data-stu-id="03f2b-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="03f2b-156">重定向到登录组件</span><span class="sxs-lookup"><span data-stu-id="03f2b-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="03f2b-157">登录显示组件</span><span class="sxs-lookup"><span data-stu-id="03f2b-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="03f2b-158">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="03f2b-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="03f2b-159">其他资源</span><span class="sxs-lookup"><span data-stu-id="03f2b-159">Additional resources</span></span>

* [<span data-ttu-id="03f2b-160">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="03f2b-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="03f2b-161">快速入门：使用 Microsoft 标识平台注册应用程序</span><span class="sxs-lookup"><span data-stu-id="03f2b-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="03f2b-162">快速入门：配置应用程序以公开 Web API</span><span class="sxs-lookup"><span data-stu-id="03f2b-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
