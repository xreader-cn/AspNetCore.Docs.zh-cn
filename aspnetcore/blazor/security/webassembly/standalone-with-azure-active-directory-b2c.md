---
title: 使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: 了解如何使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 独立应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 93644484cbff4c03fb25136afe9c1646104b652a
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712462"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e49a8-103">使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="e49a8-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e49a8-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e49a8-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e49a8-105">若要创建使用 [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) 进行身份验证的[独立 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e49a8-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="e49a8-106">请按照以下主题中的指南操作，以在 Azure 门户中创建租户并注册 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="e49a8-106">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="e49a8-107">创建 AAD B2C 租户</span><span class="sxs-lookup"><span data-stu-id="e49a8-107">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="e49a8-108">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="e49a8-108">Record the following information:</span></span>

* <span data-ttu-id="e49a8-109">AAD B2C 实例（例如 `https://contoso.b2clogin.com/`，其中包含尾部斜杠）：此实例是 Azure B2C 应用注册的方案和宿主，可以通过从 Azure 门户的“应用注册”页打开“终结点”窗口找到它。 </span><span class="sxs-lookup"><span data-stu-id="e49a8-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="e49a8-110">AAD B2C 主域/发布者域/租户域（例如 `contoso.onmicrosoft.com`）：该域在注册应用的 Azure 门户的“品牌”边栏选项卡中作为“发布者域”提供 。</span><span class="sxs-lookup"><span data-stu-id="e49a8-110">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="e49a8-111">请按照[教程：在 Azure Active Directory B2C 中注册应用程序](/azure/active-directory-b2c/tutorial-register-applications)中的指南操作，再次为客户端应用注册 AAD 应用，然后执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e49a8-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="e49a8-112">在“Azure Active Directory” > “应用注册”中，选择“新建注册”  。</span><span class="sxs-lookup"><span data-stu-id="e49a8-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="e49a8-113">提供应用的名称（例如 Blazor 独立 AAD B2C） 。</span><span class="sxs-lookup"><span data-stu-id="e49a8-113">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="e49a8-114">对于“支持的帐户类型”，请选择多租户选项：任何组织目录或任何标识提供者中的帐户。用于使用 Azure AD B2C 对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e49a8-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="e49a8-115">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="e49a8-115">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e49a8-116">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="e49a8-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e49a8-117">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="e49a8-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e49a8-118">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="e49a8-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e49a8-119">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e49a8-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e49a8-120">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e49a8-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e49a8-121">确认已启用“权限” > “授予对 openid 和 offline_access 权限的管理员同意” 。</span><span class="sxs-lookup"><span data-stu-id="e49a8-121">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e49a8-122">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="e49a8-122">Select **Register**.</span></span>

<span data-ttu-id="e49a8-123">记录应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）。</span><span class="sxs-lookup"><span data-stu-id="e49a8-123">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="e49a8-124">在“身份验证” > “平台配置” > “Web”  中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e49a8-124">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e49a8-125">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e49a8-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e49a8-126">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="e49a8-126">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e49a8-127">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="e49a8-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e49a8-128">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="e49a8-128">Select the **Save** button.</span></span>

<span data-ttu-id="e49a8-129">在“主页” > “Azure AD B2C” > “用户流”  中：</span><span class="sxs-lookup"><span data-stu-id="e49a8-129">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="e49a8-130">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="e49a8-130">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e49a8-131">至少选择“应用程序声明” > “显示名称”用户属性以填充 `LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 中的 `context.User.Identity.Name` 。</span><span class="sxs-lookup"><span data-stu-id="e49a8-131">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="e49a8-132">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin`）。</span><span class="sxs-lookup"><span data-stu-id="e49a8-132">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="e49a8-133">在空文件夹中，将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="e49a8-133">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="e49a8-134">占位符</span><span class="sxs-lookup"><span data-stu-id="e49a8-134">Placeholder</span></span>                   | <span data-ttu-id="e49a8-135">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="e49a8-135">Azure portal name</span></span>               | <span data-ttu-id="e49a8-136">示例</span><span class="sxs-lookup"><span data-stu-id="e49a8-136">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="e49a8-137">实例</span><span class="sxs-lookup"><span data-stu-id="e49a8-137">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="e49a8-138">应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="e49a8-138">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="e49a8-139">注册/登录用户流</span><span class="sxs-lookup"><span data-stu-id="e49a8-139">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="e49a8-140">主域/发布者域/租户域</span><span class="sxs-lookup"><span data-stu-id="e49a8-140">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="e49a8-141">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="e49a8-141">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e49a8-142">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置应用的“身份验证” > “平台配置” > “Web” > “重定向 URI”   。</span><span class="sxs-lookup"><span data-stu-id="e49a8-142">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="e49a8-143">如果应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的应用属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="e49a8-143">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="e49a8-144">如果端口之前未使用应用的已知端口进行配置，请返回到 Azure 门户中应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="e49a8-144">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="e49a8-145">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="e49a8-145">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e49a8-146">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="e49a8-146">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="e49a8-147">请求 Microsoft API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="e49a8-147">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="e49a8-148">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="e49a8-148">For more information, see:</span></span>
  * [<span data-ttu-id="e49a8-149">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="e49a8-149">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="e49a8-150">[快速入门：配置应用程序来公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="e49a8-150">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e49a8-151">身份验证包</span><span class="sxs-lookup"><span data-stu-id="e49a8-151">Authentication package</span></span>

<span data-ttu-id="e49a8-152">创建应用以使用个人 B2C 帐户 (`IndividualB2C`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="e49a8-152">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="e49a8-153">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="e49a8-153">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e49a8-154">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="e49a8-154">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="e49a8-155">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="e49a8-155">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="e49a8-156">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="e49a8-156">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e49a8-157">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="e49a8-157">Authentication service support</span></span>

<span data-ttu-id="e49a8-158">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="e49a8-158">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="e49a8-159">此方法设置应用与 Identity 提供者 (IP) 交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="e49a8-159">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e49a8-160">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="e49a8-160">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="e49a8-161"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="e49a8-161">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e49a8-162">注册应用时，可以从 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="e49a8-162">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="e49a8-163">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="e49a8-163">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="e49a8-164">示例：</span><span class="sxs-lookup"><span data-stu-id="e49a8-164">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="e49a8-165">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="e49a8-165">Access token scopes</span></span>

<span data-ttu-id="e49a8-166">Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="e49a8-166">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e49a8-167">要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="e49a8-167">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="e49a8-168">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="e49a8-168">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e49a8-169">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="e49a8-169">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e49a8-170">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="e49a8-170">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="e49a8-171">登录模式</span><span class="sxs-lookup"><span data-stu-id="e49a8-171">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="e49a8-172">导入文件</span><span class="sxs-lookup"><span data-stu-id="e49a8-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e49a8-173">索引页</span><span class="sxs-lookup"><span data-stu-id="e49a8-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e49a8-174">应用组件</span><span class="sxs-lookup"><span data-stu-id="e49a8-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e49a8-175">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="e49a8-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e49a8-176">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="e49a8-176">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e49a8-177">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="e49a8-177">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e49a8-178">其他资源</span><span class="sxs-lookup"><span data-stu-id="e49a8-178">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="e49a8-179">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="e49a8-179">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e49a8-180">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="e49a8-180">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="e49a8-181">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="e49a8-181">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
