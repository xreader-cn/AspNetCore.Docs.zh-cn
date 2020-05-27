---
<span data-ttu-id="04bed-101">标题： " Blazor 使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 独立应用作者：说明： monikerRange： ms. 作者： ms. 自定义：毫秒：非位置：</span><span class="sxs-lookup"><span data-stu-id="04bed-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04bed-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04bed-102">'Blazor'</span></span>
- <span data-ttu-id="04bed-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04bed-103">'Identity'</span></span>
- <span data-ttu-id="04bed-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04bed-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="04bed-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04bed-105">'Razor'</span></span>
- <span data-ttu-id="04bed-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04bed-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="04bed-107">Blazor使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="04bed-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="04bed-108">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="04bed-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="04bed-109">创建 Blazor 使用[AZURE ACTIVE DIRECTORY （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的 WebAssembly 独立应用程序：</span><span class="sxs-lookup"><span data-stu-id="04bed-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="04bed-110">[创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)：</span><span class="sxs-lookup"><span data-stu-id="04bed-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="04bed-111">在 Azure 门户的**Azure Active Directory**  >  **应用注册**区域中注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="04bed-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="04bed-112">提供应用的**名称**（例如， \*\* Blazor 独立 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="04bed-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="04bed-113">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="04bed-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="04bed-114">你**只能在此组织目录中选择帐户**。</span><span class="sxs-lookup"><span data-stu-id="04bed-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="04bed-115">将 "**重定向 uri** " 下拉菜单保留设置为 " **Web** "，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="04bed-115">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="04bed-116">在 Kestrel 上运行的应用的默认端口为5001。</span><span class="sxs-lookup"><span data-stu-id="04bed-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="04bed-117">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="04bed-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="04bed-118">对于 IIS Express，可以在 "**调试**" 面板的应用程序属性中找到应用程序的随机生成端口。</span><span class="sxs-lookup"><span data-stu-id="04bed-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="04bed-119">由于此时应用不存在，并且 IIS Express 端口未知，因此在创建应用后返回到此步骤并更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="04bed-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="04bed-120">本主题稍后会显示一个批注，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="04bed-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="04bed-121">禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="04bed-121">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="04bed-122">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="04bed-122">Select **Register**.</span></span>

<span data-ttu-id="04bed-123">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="04bed-123">Record the following information:</span></span>

* <span data-ttu-id="04bed-124">应用程序 ID （客户端 ID）（例如， `11111111-1111-1111-1111-111111111111` ）</span><span class="sxs-lookup"><span data-stu-id="04bed-124">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="04bed-125">目录 ID （租户 ID）（例如， `22222222-2222-2222-2222-222222222222` ）</span><span class="sxs-lookup"><span data-stu-id="04bed-125">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="04bed-126">在 "**身份验证**  >  **平台配置**"  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="04bed-126">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="04bed-127">确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="04bed-127">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="04bed-128">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="04bed-128">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="04bed-129">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="04bed-129">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="04bed-130">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="04bed-130">Select the **Save** button.</span></span>

<span data-ttu-id="04bed-131">创建应用。</span><span class="sxs-lookup"><span data-stu-id="04bed-131">Create the app.</span></span> <span data-ttu-id="04bed-132">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="04bed-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="04bed-133">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="04bed-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="04bed-134">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="04bed-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="04bed-135">在 Azure 门户中，将为在**Authentication**  >  **Platform configurations**  >  **Web**  >  Kestrel 服务器上运行的应用配置默认设置，为端口5001配置应用的身份验证平台配置 Web**重定向 URI** 。</span><span class="sxs-lookup"><span data-stu-id="04bed-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="04bed-136">如果应用在随机 IIS Express 端口上运行，则可在 "**调试**" 面板的应用属性中找到应用的端口。</span><span class="sxs-lookup"><span data-stu-id="04bed-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="04bed-137">如果先前未将此端口配置为应用的已知端口，请返回到 Azure 门户中的应用注册，并更新具有正确端口的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="04bed-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="04bed-138">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="04bed-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="04bed-139">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="04bed-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="04bed-140">请求 Microsoft Api 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="04bed-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="04bed-141">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="04bed-141">For more information, see:</span></span>
  * [<span data-ttu-id="04bed-142">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="04bed-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="04bed-143">[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。</span><span class="sxs-lookup"><span data-stu-id="04bed-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="04bed-144">身份验证包</span><span class="sxs-lookup"><span data-stu-id="04bed-144">Authentication package</span></span>

<span data-ttu-id="04bed-145">创建应用以使用工作或学校帐户（ `SingleOrg` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)）的包引用。</span><span class="sxs-lookup"><span data-stu-id="04bed-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="04bed-146">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="04bed-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="04bed-147">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="04bed-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="04bed-148">[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包可向应用程序中添加[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包，并将其添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="04bed-148">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="04bed-149">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="04bed-149">Authentication service support</span></span>

<span data-ttu-id="04bed-150">使用 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="04bed-150">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="04bed-151">此方法设置应用程序与 Identity 提供程序（IP）进行交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="04bed-151">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="04bed-152">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="04bed-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="04bed-153"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="04bed-153">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="04bed-154">注册应用时，可以从 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="04bed-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="04bed-155">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="04bed-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="04bed-156">示例：</span><span class="sxs-lookup"><span data-stu-id="04bed-156">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="04bed-157">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="04bed-157">Access token scopes</span></span>

<span data-ttu-id="04bed-158">BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="04bed-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="04bed-159">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="04bed-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="04bed-160">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="04bed-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="04bed-161">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="04bed-161">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="04bed-162">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="04bed-162">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="04bed-163">导入文件</span><span class="sxs-lookup"><span data-stu-id="04bed-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="04bed-164">索引页面</span><span class="sxs-lookup"><span data-stu-id="04bed-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="04bed-165">应用组件</span><span class="sxs-lookup"><span data-stu-id="04bed-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="04bed-166">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="04bed-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="04bed-167">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="04bed-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="04bed-168">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="04bed-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="04bed-169">其他资源</span><span class="sxs-lookup"><span data-stu-id="04bed-169">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="04bed-170">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="04bed-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="04bed-171">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="04bed-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
