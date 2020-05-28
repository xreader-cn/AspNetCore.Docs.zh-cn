---
<span data-ttu-id="79c6a-101">标题： " Blazor 使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 的托管应用作者：说明： monikerRange： ms. 作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="79c6a-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79c6a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79c6a-102">'Blazor'</span></span>
- <span data-ttu-id="79c6a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79c6a-103">'Identity'</span></span>
- <span data-ttu-id="79c6a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79c6a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="79c6a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79c6a-105">'Razor'</span></span>
- <span data-ttu-id="79c6a-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79c6a-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="79c6a-107">Blazor使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="79c6a-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="79c6a-108">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="79c6a-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="79c6a-109">本文介绍如何创建使用[Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的[ Blazor WebAssembly 托管应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="79c6a-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="79c6a-110">在 AAD 中注册应用并创建解决方案</span><span class="sxs-lookup"><span data-stu-id="79c6a-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="79c6a-111">创建租户</span><span class="sxs-lookup"><span data-stu-id="79c6a-111">Create a tenant</span></span>

<span data-ttu-id="79c6a-112">按照[快速入门：设置租户](/azure/active-directory/develop/quickstart-create-new-tenant)中的指导在 AAD 中创建租户。</span><span class="sxs-lookup"><span data-stu-id="79c6a-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="79c6a-113">注册服务器 API 应用</span><span class="sxs-lookup"><span data-stu-id="79c6a-113">Register a server API app</span></span>

<span data-ttu-id="79c6a-114">按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题，为*服务器 API 应用*注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="79c6a-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="79c6a-115">在**Azure Active Directory**  >  **应用注册**中，选择 "**新建注册**"。</span><span class="sxs-lookup"><span data-stu-id="79c6a-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="79c6a-116">提供应用的**名称**（例如， \*\* Blazor 服务器 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="79c6a-117">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="79c6a-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="79c6a-118">对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="79c6a-119">在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。</span><span class="sxs-lookup"><span data-stu-id="79c6a-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="79c6a-120">禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="79c6a-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="79c6a-121">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="79c6a-121">Select **Register**.</span></span>

<span data-ttu-id="79c6a-122">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="79c6a-122">Record the following information:</span></span>

* <span data-ttu-id="79c6a-123">*服务器 API 应用*应用程序 ID （客户端 ID）（例如， `11111111-1111-1111-1111-111111111111` ）</span><span class="sxs-lookup"><span data-stu-id="79c6a-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="79c6a-124">目录 ID （租户 ID）（例如， `222222222-2222-2222-2222-222222222222` ）</span><span class="sxs-lookup"><span data-stu-id="79c6a-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="79c6a-125">AAD 租户域（例如， `contoso.onmicrosoft.com` ）：在注册的应用的 Azure 门户的 "**品牌**" 边栏选项卡中，该域作为**发布者域**提供。</span><span class="sxs-lookup"><span data-stu-id="79c6a-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="79c6a-126">在 " **API 权限**" 中，删除**Microsoft Graph**  >  **用户. 读取**权限，因为应用无需登录或用户配置文件访问。</span><span class="sxs-lookup"><span data-stu-id="79c6a-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="79c6a-127">在中**公开 API**：</span><span class="sxs-lookup"><span data-stu-id="79c6a-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="79c6a-128">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="79c6a-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="79c6a-129">选择“保存并继续”。 </span><span class="sxs-lookup"><span data-stu-id="79c6a-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="79c6a-130">提供**作用域名称**（例如 `API.Access` ）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="79c6a-131">提供**管理员同意显示名称**（例如 `Access API` ）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="79c6a-132">提供**管理员同意说明**（例如 `Allows the app to access server app API endpoints.` ）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="79c6a-133">确认 "**状态**" 设置为 "**已启用**"。</span><span class="sxs-lookup"><span data-stu-id="79c6a-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="79c6a-134">选择“添加范围”。</span><span class="sxs-lookup"><span data-stu-id="79c6a-134">Select **Add scope**.</span></span>

<span data-ttu-id="79c6a-135">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="79c6a-135">Record the following information:</span></span>

* <span data-ttu-id="79c6a-136">应用 ID URI （例如，、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` 或提供的自定义值）</span><span class="sxs-lookup"><span data-stu-id="79c6a-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="79c6a-137">默认作用域（例如 `API.Access` ）</span><span class="sxs-lookup"><span data-stu-id="79c6a-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="79c6a-138">注册客户端应用</span><span class="sxs-lookup"><span data-stu-id="79c6a-138">Register a client app</span></span>

<span data-ttu-id="79c6a-139">按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，为*客户端应用程序*注册 AAD 应用程序：</span><span class="sxs-lookup"><span data-stu-id="79c6a-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="79c6a-140">在**Azure Active Directory**  >  **应用注册**中，选择 "**新建注册**"。</span><span class="sxs-lookup"><span data-stu-id="79c6a-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="79c6a-141">提供应用的**名称**（例如， \*\* Blazor 客户端 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="79c6a-142">选择**受支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="79c6a-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="79c6a-143">对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="79c6a-144">将 "**重定向 uri** " 下拉菜单保留设置为 " **Web** "，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="79c6a-144">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="79c6a-145">在 Kestrel 上运行的应用的默认端口为5001。</span><span class="sxs-lookup"><span data-stu-id="79c6a-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="79c6a-146">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="79c6a-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="79c6a-147">对于 IIS Express，可以在 "**调试**" 面板的服务器应用的属性中找到该应用的随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="79c6a-147">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="79c6a-148">由于此时应用不存在，并且 IIS Express 端口未知，因此在创建应用后返回到此步骤并更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="79c6a-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="79c6a-149">"[创建应用"](#create-the-app)部分中将出现一个批注，以提醒 IIS Express 的用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="79c6a-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="79c6a-150">禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="79c6a-150">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="79c6a-151">选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="79c6a-151">Select **Register**.</span></span>

<span data-ttu-id="79c6a-152">记录*客户端应用*应用程序 Id （客户端 id）（例如 `33333333-3333-3333-3333-333333333333` ）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-152">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="79c6a-153">在 "**身份验证**  >  **平台配置**"  >  **Web**：</span><span class="sxs-lookup"><span data-stu-id="79c6a-153">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="79c6a-154">确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="79c6a-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="79c6a-155">对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。</span><span class="sxs-lookup"><span data-stu-id="79c6a-155">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="79c6a-156">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="79c6a-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="79c6a-157">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="79c6a-157">Select the **Save** button.</span></span>

<span data-ttu-id="79c6a-158">在 " **API 权限**：</span><span class="sxs-lookup"><span data-stu-id="79c6a-158">In **API permissions**:</span></span>

1. <span data-ttu-id="79c6a-159">确认应用程序具有**Microsoft Graph**的 "  >  **用户**" 权限。</span><span class="sxs-lookup"><span data-stu-id="79c6a-159">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="79c6a-160">选择 "**添加权限**"，然后选择 **"我的 api"**。</span><span class="sxs-lookup"><span data-stu-id="79c6a-160">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="79c6a-161">从 "**名称**" 列中选择*服务器 API 应用*（例如， \*\* Blazor 服务器 AAD\*\*）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-161">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="79c6a-162">打开**API**列表。</span><span class="sxs-lookup"><span data-stu-id="79c6a-162">Open the **API** list.</span></span>
1. <span data-ttu-id="79c6a-163">启用对 API 的访问（例如 `API.Access` ）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-163">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="79c6a-164">选择“添加权限”。</span><span class="sxs-lookup"><span data-stu-id="79c6a-164">Select **Add permissions**.</span></span>
1. <span data-ttu-id="79c6a-165">选择 "**为 {租户名称} 授予管理内容**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="79c6a-165">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="79c6a-166">请选择“是”以确认。 </span><span class="sxs-lookup"><span data-stu-id="79c6a-166">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="79c6a-167">创建应用程序</span><span class="sxs-lookup"><span data-stu-id="79c6a-167">Create the app</span></span>

<span data-ttu-id="79c6a-168">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：</span><span class="sxs-lookup"><span data-stu-id="79c6a-168">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="79c6a-169">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="79c6a-169">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="79c6a-170">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="79c6a-170">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="79c6a-171">将应用 ID URI 传递给 `app-id-uri` 选项，但请注意，可能需要在客户端应用中进行配置更改，这在 "[访问令牌范围](#access-token-scopes)" 部分中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="79c6a-171">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="79c6a-172">在 Azure 门户中，*客户端应用的\*\*\*身份验证*\*  >  **平台配置**  >  **Web**  >  **重定向 URI**为端口5001配置，适用于在 Kestrel 服务器上用默认设置运行的应用。</span><span class="sxs-lookup"><span data-stu-id="79c6a-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="79c6a-173">如果*客户端应用*是在随机 IIS Express 端口上运行，则可以在 "**调试**" 面板中的*服务器应用*的属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="79c6a-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="79c6a-174">如果先前未将此端口配置为*客户端应用的*已知端口，请返回到 Azure 门户中的*客户端应用*注册，并更新具有正确端口的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="79c6a-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="79c6a-175">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="79c6a-175">Server app configuration</span></span>

<span data-ttu-id="79c6a-176">*本部分适用于解决方案的**服务器**应用。*</span><span class="sxs-lookup"><span data-stu-id="79c6a-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="79c6a-177">身份验证包</span><span class="sxs-lookup"><span data-stu-id="79c6a-177">Authentication package</span></span>

<span data-ttu-id="79c6a-178">对 ASP.NET Core Web Api 进行身份验证和授权的支持是由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/)包提供的：</span><span class="sxs-lookup"><span data-stu-id="79c6a-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="79c6a-179">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="79c6a-179">Authentication service support</span></span>

<span data-ttu-id="79c6a-180">`AddAuthentication`方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认的身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="79c6a-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="79c6a-181"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>方法在验证 Azure Active Directory 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：</span><span class="sxs-lookup"><span data-stu-id="79c6a-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="79c6a-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>并 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 确保：</span><span class="sxs-lookup"><span data-stu-id="79c6a-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="79c6a-183">应用尝试分析和验证传入请求的令牌。</span><span class="sxs-lookup"><span data-stu-id="79c6a-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="79c6a-184">任何试图访问受保护资源的请求均不正确。</span><span class="sxs-lookup"><span data-stu-id="79c6a-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="79c6a-185">用户。 Identity路径名</span><span class="sxs-lookup"><span data-stu-id="79c6a-185">User.Identity.Name</span></span>

<span data-ttu-id="79c6a-186">默认情况下，服务器应用 API `User.Identity.Name` 使用声明类型中的值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` （例如）进行填充 `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` 。</span><span class="sxs-lookup"><span data-stu-id="79c6a-186">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="79c6a-187">若要将应用配置为接收来自 `name` 声明类型的值，请在中配置[TokenValidationParameters](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="79c6a-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="79c6a-188">应用设置</span><span class="sxs-lookup"><span data-stu-id="79c6a-188">App settings</span></span>

<span data-ttu-id="79c6a-189">*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项：</span><span class="sxs-lookup"><span data-stu-id="79c6a-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="79c6a-190">示例：</span><span class="sxs-lookup"><span data-stu-id="79c6a-190">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="79c6a-191">WeatherForecast 控制器</span><span class="sxs-lookup"><span data-stu-id="79c6a-191">WeatherForecast controller</span></span>

<span data-ttu-id="79c6a-192">WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，该 API 的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="79c6a-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="79c6a-193">**务必**要了解：</span><span class="sxs-lookup"><span data-stu-id="79c6a-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="79c6a-194">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)此 api 控制器中的属性只是保护此 api 不受未经授权的访问。</span><span class="sxs-lookup"><span data-stu-id="79c6a-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="79c6a-195">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)WebAssembly 应用程序中使用的属性 Blazor 仅作为对应用程序的提示，用户应授权该应用程序正常工作。</span><span class="sxs-lookup"><span data-stu-id="79c6a-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="79c6a-196">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="79c6a-196">Client app configuration</span></span>

<span data-ttu-id="79c6a-197">*本部分适用于解决方案的**客户端**应用。*</span><span class="sxs-lookup"><span data-stu-id="79c6a-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="79c6a-198">身份验证包</span><span class="sxs-lookup"><span data-stu-id="79c6a-198">Authentication package</span></span>

<span data-ttu-id="79c6a-199">创建应用以使用工作或学校帐户（ `SingleOrg` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)）的包引用。</span><span class="sxs-lookup"><span data-stu-id="79c6a-199">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="79c6a-200">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="79c6a-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="79c6a-201">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="79c6a-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="79c6a-202">[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包可向应用程序中添加[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包，并将其添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="79c6a-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="79c6a-203">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="79c6a-203">Authentication service support</span></span>

<span data-ttu-id="79c6a-204">添加了对实例的支持 <xref:System.Net.Http.HttpClient> ，其中包括对服务器项目发出请求时的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="79c6a-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="79c6a-205">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="79c6a-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="79c6a-206">使用 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="79c6a-206">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="79c6a-207">此方法设置应用程序与 Identity 提供程序（IP）进行交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="79c6a-207">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="79c6a-208">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="79c6a-208">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="79c6a-209"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法接受回调，以配置对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="79c6a-209">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="79c6a-210">注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="79c6a-210">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="79c6a-211">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="79c6a-211">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="79c6a-212">示例：</span><span class="sxs-lookup"><span data-stu-id="79c6a-212">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="79c6a-213">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="79c6a-213">Access token scopes</span></span>

<span data-ttu-id="79c6a-214">默认访问令牌范围表示访问令牌作用域的列表：</span><span class="sxs-lookup"><span data-stu-id="79c6a-214">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="79c6a-215">默认情况下，在登录请求中包括。</span><span class="sxs-lookup"><span data-stu-id="79c6a-215">Included by default in the sign in request.</span></span>
* <span data-ttu-id="79c6a-216">用于在身份验证后立即设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="79c6a-216">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="79c6a-217">对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。</span><span class="sxs-lookup"><span data-stu-id="79c6a-217">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="79c6a-218">可以根据需要为其他 API 应用添加其他作用域：</span><span class="sxs-lookup"><span data-stu-id="79c6a-218">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="79c6a-219">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="79c6a-219">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="79c6a-220">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="79c6a-220">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="79c6a-221">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="79c6a-221">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="79c6a-222">导入文件</span><span class="sxs-lookup"><span data-stu-id="79c6a-222">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="79c6a-223">索引页面</span><span class="sxs-lookup"><span data-stu-id="79c6a-223">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="79c6a-224">应用组件</span><span class="sxs-lookup"><span data-stu-id="79c6a-224">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="79c6a-225">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="79c6a-225">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="79c6a-226">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="79c6a-226">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="79c6a-227">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="79c6a-227">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="79c6a-228">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="79c6a-228">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="79c6a-229">运行应用</span><span class="sxs-lookup"><span data-stu-id="79c6a-229">Run the app</span></span>

<span data-ttu-id="79c6a-230">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="79c6a-230">Run the app from the Server project.</span></span> <span data-ttu-id="79c6a-231">使用 Visual Studio 时，可以执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="79c6a-231">When using Visual Studio, either:</span></span>

* <span data-ttu-id="79c6a-232">将工具栏中的 "**启动项目**" 下拉列表设置为 "*服务器 API 应用*"，然后选择 "**运行**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="79c6a-232">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="79c6a-233">在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单中启动该应用程序。</span><span class="sxs-lookup"><span data-stu-id="79c6a-233">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="79c6a-234">其他资源</span><span class="sxs-lookup"><span data-stu-id="79c6a-234">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="79c6a-235">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="79c6a-235">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="79c6a-236">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="79c6a-236">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
