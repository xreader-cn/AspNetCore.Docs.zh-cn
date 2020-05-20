---
<span data-ttu-id="fd3f9-101">标题： " Blazor 使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 独立应用作者：说明： monikerRange： ms. 作者： ms. 自定义：毫秒：非位置：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fd3f9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fd3f9-102">'Blazor'</span></span>
- <span data-ttu-id="fd3f9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fd3f9-103">'Identity'</span></span>
- <span data-ttu-id="fd3f9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fd3f9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fd3f9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fd3f9-105">'Razor'</span></span>
- <span data-ttu-id="fd3f9-106">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="fd3f9-107">Blazor使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="fd3f9-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="fd3f9-108">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fd3f9-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fd3f9-109">创建 Blazor 使用[AZURE ACTIVE DIRECTORY （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的 WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="fd3f9-110">[创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="fd3f9-111">在 Azure 门户的**Azure Active Directory**  >  **应用注册**区域中注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fd3f9-112">提供应用的**名称**（例如， \*\* Blazor 独立 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="fd3f9-113">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="fd3f9-114">你**只能在此组织目录中选择帐户**。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="fd3f9-115">将 "**重定向 uri** " 下拉项设置为 " **Web**"，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="fd3f9-116">在 Kestrel 上运行的应用的默认端口为5001。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="fd3f9-117">对于 IIS Express，可以在 "**调试**" 面板的应用程序属性中找到随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="fd3f9-118">禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="fd3f9-119">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-119">Select **Register**.</span></span>

<span data-ttu-id="fd3f9-120">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-120">Record the following information:</span></span>

* <span data-ttu-id="fd3f9-121">应用程序 ID （客户端 ID）（例如， `11111111-1111-1111-1111-111111111111` ）</span><span class="sxs-lookup"><span data-stu-id="fd3f9-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="fd3f9-122">目录 ID （租户 ID）（例如， `22222222-2222-2222-2222-222222222222` ）</span><span class="sxs-lookup"><span data-stu-id="fd3f9-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="fd3f9-123">在 "**身份验证**  >  **平台配置**"  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="fd3f9-124">确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="fd3f9-125">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="fd3f9-126">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="fd3f9-127">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-127">Select the **Save** button.</span></span>

<span data-ttu-id="fd3f9-128">创建应用。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-128">Create the app.</span></span> <span data-ttu-id="fd3f9-129">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="fd3f9-130">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fd3f9-131">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="fd3f9-132">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="fd3f9-133">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="fd3f9-134">请求 Microsoft Api 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="fd3f9-135">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="fd3f9-135">For more information, see:</span></span>
  * [<span data-ttu-id="fd3f9-136">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="fd3f9-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="fd3f9-137">[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fd3f9-138">身份验证包</span><span class="sxs-lookup"><span data-stu-id="fd3f9-138">Authentication package</span></span>

<span data-ttu-id="fd3f9-139">创建应用以使用工作或学校帐户（ `SingleOrg` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（）的包引用 `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fd3f9-140">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fd3f9-141">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="fd3f9-142">`Microsoft.Authentication.WebAssembly.Msal`包可传递将 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fd3f9-143">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="fd3f9-143">Authentication service support</span></span>

<span data-ttu-id="fd3f9-144">使用包提供的扩展方法在服务容器中注册对用户进行身份验证的支持 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fd3f9-145">此方法设置应用程序与 Identity 提供程序（IP）进行交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fd3f9-146">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="fd3f9-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="fd3f9-147">`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fd3f9-148">注册应用时，可以从 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="fd3f9-149">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="fd3f9-150">示例：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="fd3f9-151">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="fd3f9-151">Access token scopes</span></span>

<span data-ttu-id="fd3f9-152">BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="fd3f9-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fd3f9-153">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中 `MsalProviderOptions` ：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="fd3f9-154">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="fd3f9-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="fd3f9-155">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="fd3f9-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="fd3f9-156">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="fd3f9-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="fd3f9-157">导入文件</span><span class="sxs-lookup"><span data-stu-id="fd3f9-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="fd3f9-158">索引页面</span><span class="sxs-lookup"><span data-stu-id="fd3f9-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="fd3f9-159">应用组件</span><span class="sxs-lookup"><span data-stu-id="fd3f9-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fd3f9-160">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="fd3f9-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fd3f9-161">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="fd3f9-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fd3f9-162">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="fd3f9-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fd3f9-163">其他资源</span><span class="sxs-lookup"><span data-stu-id="fd3f9-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="fd3f9-164">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="fd3f9-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="fd3f9-165">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="fd3f9-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
