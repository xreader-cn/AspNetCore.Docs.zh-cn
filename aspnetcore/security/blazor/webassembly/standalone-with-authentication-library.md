---
<span data-ttu-id="29125-101">标题： ' Blazor 使用身份验证库的 Secure a ASP.NET Core WebAssembly 独立应用作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非位置：</span><span class="sxs-lookup"><span data-stu-id="29125-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29125-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29125-102">'Blazor'</span></span>
- <span data-ttu-id="29125-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29125-103">'Identity'</span></span>
- <span data-ttu-id="29125-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29125-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="29125-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29125-105">'Razor'</span></span>
- <span data-ttu-id="29125-106">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="29125-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="29125-107">Blazor使用身份验证库保护 ASP.NET Core WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="29125-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="29125-108">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29125-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="29125-109">*对于 Azure Active Directory （AAD）和 Azure Active Directory B2C （AAD B2C），请勿按照本主题中的指导进行操作。请参阅此目录节点中的 AAD 和 AAD B2C 主题。*</span><span class="sxs-lookup"><span data-stu-id="29125-109">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="29125-110">若要创建 Blazor 使用库的 WebAssembly 独立应用程序 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="29125-110">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="29125-111">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。</span><span class="sxs-lookup"><span data-stu-id="29125-111">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="29125-112">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="29125-112">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="29125-113">在 Visual Studio 中，[创建一个 Blazor WebAssembly 应用](xref:blazor/get-started)。</span><span class="sxs-lookup"><span data-stu-id="29125-113">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="29125-114">将**身份验证**设置为具有 "**应用商店用户帐户应用内**" 选项的**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="29125-114">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="29125-115">身份验证包</span><span class="sxs-lookup"><span data-stu-id="29125-115">Authentication package</span></span>

<span data-ttu-id="29125-116">创建应用以使用单个用户帐户时，应用会 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 在应用的项目文件中自动接收包的包引用。</span><span class="sxs-lookup"><span data-stu-id="29125-116">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="29125-117">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="29125-117">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="29125-118">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="29125-118">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="29125-119">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="29125-119">Authentication service support</span></span>

<span data-ttu-id="29125-120">使用包提供的扩展方法在服务容器中注册对用户进行身份验证的支持 `AddOidcAuthentication` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 。</span><span class="sxs-lookup"><span data-stu-id="29125-120">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="29125-121">此方法设置应用程序与 Identity 提供程序（IP）进行交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="29125-121">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="29125-122">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="29125-122">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="29125-123">配置由*wwwroot/appsettings*文件提供：</span><span class="sxs-lookup"><span data-stu-id="29125-123">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="29125-124">使用 Open ID Connect （OIDC）提供对独立应用程序的身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="29125-124">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="29125-125">`AddOidcAuthentication`方法接受回调来配置使用 OIDC 对应用进行身份验证所需的参数。</span><span class="sxs-lookup"><span data-stu-id="29125-125">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="29125-126">配置应用所需的值可以从 OIDC 兼容的 IP 获得。</span><span class="sxs-lookup"><span data-stu-id="29125-126">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="29125-127">注册应用时获取值，此操作通常发生在其联机门户中。</span><span class="sxs-lookup"><span data-stu-id="29125-127">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="29125-128">访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="29125-128">Access token scopes</span></span>

<span data-ttu-id="29125-129">BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="29125-129">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="29125-130">若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认令牌范围中 `OidcProviderOptions` ：</span><span class="sxs-lookup"><span data-stu-id="29125-130">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="29125-131">有关详细信息，请参阅*其他方案*一文中的以下部分：</span><span class="sxs-lookup"><span data-stu-id="29125-131">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="29125-132">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="29125-132">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="29125-133">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="29125-133">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="29125-134">导入文件</span><span class="sxs-lookup"><span data-stu-id="29125-134">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="29125-135">索引页面</span><span class="sxs-lookup"><span data-stu-id="29125-135">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="29125-136">应用组件</span><span class="sxs-lookup"><span data-stu-id="29125-136">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="29125-137">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="29125-137">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="29125-138">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="29125-138">LoginDisplay component</span></span>

<span data-ttu-id="29125-139">`LoginDisplay`组件（*Shared/LoginDisplay*）在 `MainLayout` 组件（*shared/MainLayout*）中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="29125-139">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="29125-140">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="29125-140">For authenticated users:</span></span>
  * <span data-ttu-id="29125-141">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="29125-141">Displays the current username.</span></span>
  * <span data-ttu-id="29125-142">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="29125-142">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="29125-143">对于匿名用户，提供登录选项。</span><span class="sxs-lookup"><span data-stu-id="29125-143">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="29125-144">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="29125-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="29125-145">其他资源</span><span class="sxs-lookup"><span data-stu-id="29125-145">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="29125-146">使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求</span><span class="sxs-lookup"><span data-stu-id="29125-146">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
