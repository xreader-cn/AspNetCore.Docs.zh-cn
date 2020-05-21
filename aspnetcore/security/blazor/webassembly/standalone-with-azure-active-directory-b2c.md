---
<span data-ttu-id="d643c-101">标题： " Blazor 使用 Azure Active Directory B2C 保护 ASP.NET Core WebAssembly 独立应用作者：说明： monikerRange： ms. 作者： ms. 自定义：毫秒：非位置：</span><span class="sxs-lookup"><span data-stu-id="d643c-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d643c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d643c-102">'Blazor'</span></span>
- <span data-ttu-id="d643c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d643c-103">'Identity'</span></span>
- <span data-ttu-id="d643c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d643c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d643c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d643c-105">'Razor'</span></span>
- <span data-ttu-id="d643c-106">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d643c-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="d643c-107">Blazor使用 Azure Active Directory B2C 保护 ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="d643c-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="d643c-108">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d643c-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d643c-109">创建 Blazor 使用[AZURE ACTIVE DIRECTORY （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="d643c-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="d643c-110">按照以下主题中的指导在 Azure 门户中创建租户并注册 web 应用：</span><span class="sxs-lookup"><span data-stu-id="d643c-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="d643c-111">创建 AAD B2C 租户</span><span class="sxs-lookup"><span data-stu-id="d643c-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="d643c-112">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="d643c-112">Record the following information:</span></span>

* <span data-ttu-id="d643c-113">AAD B2C 实例（例如， `https://contoso.b2clogin.com/` 包括尾部斜杠）。</span><span class="sxs-lookup"><span data-stu-id="d643c-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="d643c-114">AAD B2C 租户域（例如 `contoso.onmicrosoft.com` ）。</span><span class="sxs-lookup"><span data-stu-id="d643c-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="d643c-115">遵循[教程：将应用程序注册到 Azure Active Directory B2C 中](/azure/active-directory-b2c/tutorial-register-applications)的指南，以便为*客户端应用程序*注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="d643c-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="d643c-116">在**Azure Active Directory**  >  **应用注册**中，选择 "**新建注册**"。</span><span class="sxs-lookup"><span data-stu-id="d643c-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="d643c-117">提供应用的**名称**（例如， \*\* Blazor 独立 AAD B2C\*\*）。</span><span class="sxs-lookup"><span data-stu-id="d643c-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="d643c-118">对于 "**支持的帐户类型**"，请选择 "多租户" 选项：**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**</span><span class="sxs-lookup"><span data-stu-id="d643c-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="d643c-119">将 "**重定向 uri** " 下拉菜单保留设置为 " **Web** "，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="d643c-119">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="d643c-120">在 Kestrel 上运行的应用的默认端口为5001。</span><span class="sxs-lookup"><span data-stu-id="d643c-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="d643c-121">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="d643c-121">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="d643c-122">对于 IIS Express，可以在 "**调试**" 面板的应用程序属性中找到应用程序的随机生成端口。</span><span class="sxs-lookup"><span data-stu-id="d643c-122">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="d643c-123">由于此时应用不存在，并且 IIS Express 端口未知，因此在创建应用后返回到此步骤并更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="d643c-123">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="d643c-124">本主题稍后会显示一个批注，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="d643c-124">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="d643c-125">确认**权限**"  >  **授予管理员以免到 openid" 和 "offline_access" 权限**已启用。</span><span class="sxs-lookup"><span data-stu-id="d643c-125">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="d643c-126">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="d643c-126">Select **Register**.</span></span>

<span data-ttu-id="d643c-127">记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111` ）。</span><span class="sxs-lookup"><span data-stu-id="d643c-127">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="d643c-128">在 "**身份验证**  >  **平台配置**"  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="d643c-128">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="d643c-129">确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="d643c-129">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="d643c-130">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="d643c-130">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="d643c-131">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="d643c-131">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="d643c-132">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="d643c-132">Select the **Save** button.</span></span>

<span data-ttu-id="d643c-133">在**家庭**  >  **Azure AD B2C**  >  **用户流**：</span><span class="sxs-lookup"><span data-stu-id="d643c-133">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="d643c-134">创建注册和登录用户流</span><span class="sxs-lookup"><span data-stu-id="d643c-134">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="d643c-135">至少，选择 "**应用程序声明**  >  **显示名称**用户" 属性以 `context.User.Identity.Name` 在组件中填充 `LoginDisplay` （*Shared/LoginDisplay*）。</span><span class="sxs-lookup"><span data-stu-id="d643c-135">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="d643c-136">记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin` ）。</span><span class="sxs-lookup"><span data-stu-id="d643c-136">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="d643c-137">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="d643c-137">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="d643c-138">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="d643c-138">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d643c-139">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="d643c-139">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="d643c-140">在 Azure 门户中，将为在**Authentication**  >  **Platform configurations**  >  **Web**  >  Kestrel 服务器上运行的应用配置默认设置，为端口5001配置应用的身份验证平台配置 Web**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="d643c-140">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="d643c-141">如果应用在随机 IIS Express 端口上运行，则可在 "**调试**" 面板的应用属性中找到应用的端口。</span><span class="sxs-lookup"><span data-stu-id="d643c-141">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="d643c-142">如果先前未将此端口配置为应用的已知端口，请返回到 Azure 门户中的应用注册，并更新具有正确端口的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="d643c-142">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="d643c-143">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="d643c-143">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="d643c-144">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="d643c-144">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="d643c-145">请求 Microsoft Api 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d643c-145">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="d643c-146">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="d643c-146">For more information, see:</span></span>
  * [<span data-ttu-id="d643c-147">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="d643c-147">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="d643c-148">[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="d643c-148">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="d643c-149">身份验证包</span><span class="sxs-lookup"><span data-stu-id="d643c-149">Authentication package</span></span>

<span data-ttu-id="d643c-150">当创建应用以使用单个 B2C 帐户（ `IndividualB2C` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（）的包引用 `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="d643c-150">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="d643c-151">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="d643c-151">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d643c-152">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="d643c-152">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="d643c-153">`Microsoft.Authentication.WebAssembly.Msal`包可传递将 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="d643c-153">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="d643c-154">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="d643c-154">Authentication service support</span></span>

<span data-ttu-id="d643c-155">使用包提供的扩展方法在服务容器中注册对用户进行身份验证的支持 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="d643c-155">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="d643c-156">此方法设置应用与 Identity 提供程序（IP）交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="d643c-156">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d643c-157">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="d643c-157">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="d643c-158">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="d643c-158">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d643c-159">注册应用时，可以从 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="d643c-159">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="d643c-160">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="d643c-160">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="d643c-161">示例：</span><span class="sxs-lookup"><span data-stu-id="d643c-161">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="d643c-162">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="d643c-162">Access token scopes</span></span>

<span data-ttu-id="d643c-163">BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d643c-163">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="d643c-164">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中 `MsalProviderOptions` ：</span><span class="sxs-lookup"><span data-stu-id="d643c-164">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="d643c-165">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="d643c-165">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="d643c-166">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="d643c-166">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="d643c-167">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="d643c-167">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="d643c-168">导入文件</span><span class="sxs-lookup"><span data-stu-id="d643c-168">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="d643c-169">索引页面</span><span class="sxs-lookup"><span data-stu-id="d643c-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="d643c-170">应用组件</span><span class="sxs-lookup"><span data-stu-id="d643c-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="d643c-171">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="d643c-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="d643c-172">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="d643c-172">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="d643c-173">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="d643c-173">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d643c-174">其他资源</span><span class="sxs-lookup"><span data-stu-id="d643c-174">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="d643c-175">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="d643c-175">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="d643c-176">教程：创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="d643c-176">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="d643c-177">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="d643c-177">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
