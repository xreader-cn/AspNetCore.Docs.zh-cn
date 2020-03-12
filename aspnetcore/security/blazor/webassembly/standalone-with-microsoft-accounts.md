---
title: 使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6883af3486256e7c6905626d8da09e8ae0c4a896
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083649"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="1948b-102">使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="1948b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="1948b-103">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1948b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1948b-104">若要创建一个使用[Microsoft Azure Active Directory 帐户](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)进行身份验证的 Blazor WebAssembly 独立应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1948b-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="1948b-105">创建 AAD 租户和 web 应用程序</span><span class="sxs-lookup"><span data-stu-id="1948b-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="1948b-106">在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="1948b-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="1948b-107">1 \。</span><span class="sxs-lookup"><span data-stu-id="1948b-107">1\.</span></span> <span data-ttu-id="1948b-108">提供应用的**名称**（例如， **Blazor 客户端 AAD**）。</span><span class="sxs-lookup"><span data-stu-id="1948b-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="1948b-109">2。</span><span class="sxs-lookup"><span data-stu-id="1948b-109">2\.</span></span> <span data-ttu-id="1948b-110">在 "**支持的帐户类型**" 中，选择**任何组织目录中的帐户**。</span><span class="sxs-lookup"><span data-stu-id="1948b-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="1948b-111">3。</span><span class="sxs-lookup"><span data-stu-id="1948b-111">3\.</span></span> <span data-ttu-id="1948b-112">将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。</span><span class="sxs-lookup"><span data-stu-id="1948b-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="1948b-113">4 \。</span><span class="sxs-lookup"><span data-stu-id="1948b-113">4\.</span></span> <span data-ttu-id="1948b-114">禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。</span><span class="sxs-lookup"><span data-stu-id="1948b-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="1948b-115">5 \。</span><span class="sxs-lookup"><span data-stu-id="1948b-115">5\.</span></span> <span data-ttu-id="1948b-116">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="1948b-116">Select **Register**.</span></span>

   <span data-ttu-id="1948b-117">在 "**身份验证**" > **平台配置** > **Web**：</span><span class="sxs-lookup"><span data-stu-id="1948b-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="1948b-118">1 \。</span><span class="sxs-lookup"><span data-stu-id="1948b-118">1\.</span></span> <span data-ttu-id="1948b-119">确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="1948b-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="1948b-120">2。</span><span class="sxs-lookup"><span data-stu-id="1948b-120">2\.</span></span> <span data-ttu-id="1948b-121">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="1948b-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="1948b-122">3。</span><span class="sxs-lookup"><span data-stu-id="1948b-122">3\.</span></span> <span data-ttu-id="1948b-123">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="1948b-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="1948b-124">4 \。</span><span class="sxs-lookup"><span data-stu-id="1948b-124">4\.</span></span> <span data-ttu-id="1948b-125">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="1948b-125">Select the **Save** button.</span></span>

   <span data-ttu-id="1948b-126">记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）。</span><span class="sxs-lookup"><span data-stu-id="1948b-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="1948b-127">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="1948b-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="1948b-128">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="1948b-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1948b-129">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="1948b-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="1948b-130">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="1948b-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="1948b-131">使用 Microsoft 帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="1948b-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="1948b-132">使用与独立 Blazor 应用相同的方法为 Microsoft Api 请求访问令牌，前提是已正确配置应用。</span><span class="sxs-lookup"><span data-stu-id="1948b-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="1948b-133">有关详细信息，请参阅[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="1948b-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1948b-134">身份验证包</span><span class="sxs-lookup"><span data-stu-id="1948b-134">Authentication package</span></span>

<span data-ttu-id="1948b-135">创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。</span><span class="sxs-lookup"><span data-stu-id="1948b-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1948b-136">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="1948b-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1948b-137">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="1948b-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1948b-138">将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。</span><span class="sxs-lookup"><span data-stu-id="1948b-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1948b-139">`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。</span><span class="sxs-lookup"><span data-stu-id="1948b-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1948b-140">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="1948b-140">Authentication service support</span></span>

<span data-ttu-id="1948b-141">使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="1948b-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1948b-142">此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="1948b-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1948b-143">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="1948b-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="1948b-144">`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="1948b-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1948b-145">注册应用时，可以从 Microsoft 帐户配置获取配置该应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="1948b-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="1948b-146">索引页</span><span class="sxs-lookup"><span data-stu-id="1948b-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="1948b-147">应用组件</span><span class="sxs-lookup"><span data-stu-id="1948b-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1948b-148">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="1948b-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1948b-149">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="1948b-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1948b-150">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="1948b-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1948b-151">其他资源</span><span class="sxs-lookup"><span data-stu-id="1948b-151">Additional resources</span></span>

* [<span data-ttu-id="1948b-152">快速入门：向 Microsoft 标识平台注册应用程序</span><span class="sxs-lookup"><span data-stu-id="1948b-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="1948b-153">快速入门：将应用程序配置为公开 web Api</span><span class="sxs-lookup"><span data-stu-id="1948b-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
