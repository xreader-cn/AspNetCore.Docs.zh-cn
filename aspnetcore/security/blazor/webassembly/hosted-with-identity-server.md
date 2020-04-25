---
title: 使用标识服务器Blazor保护 ASP.NET Core WebAssembly 托管应用
author: guardrex
description: 使用 IdentityServer 后端Blazor在 Visual Studio 中创建具有身份验证的新托管[IdentityServer](https://identityserver.io/)应用程序
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ffdcd30ae9ce5350113569a500e99cf8db82ad65
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138596"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="bb692-103">使用标识服务器Blazor保护 ASP.NET Core WebAssembly 托管应用</span><span class="sxs-lookup"><span data-stu-id="bb692-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="bb692-104">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb692-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="bb692-105">若要在 Visual Blazor Studio 中创建新的托管应用，以便使用[IdentityServer](https://identityserver.io/)对用户和 API 调用进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="bb692-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="bb692-106">使用 Visual Studio 创建新\*\* Blazor的 WebAssembly\*\*应用。</span><span class="sxs-lookup"><span data-stu-id="bb692-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="bb692-107">有关详细信息，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="bb692-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="bb692-108">在 "**创建新Blazor应用**" 对话框中，在 "**身份验证**" 部分中选择 "**更改**"。</span><span class="sxs-lookup"><span data-stu-id="bb692-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="bb692-109">选择后跟 **"确定"** 的**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="bb692-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="bb692-110">选中 "**高级**" 部分中的 " **ASP.NET Core 托管**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="bb692-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="bb692-111">选择“创建”\*\*\*\* 按钮。</span><span class="sxs-lookup"><span data-stu-id="bb692-111">Select the **Create** button.</span></span>

<span data-ttu-id="bb692-112">若要在命令外壳中创建应用，请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bb692-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="bb692-113">若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。</span><span class="sxs-lookup"><span data-stu-id="bb692-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="bb692-114">文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="bb692-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="bb692-115">服务器应用配置</span><span class="sxs-lookup"><span data-stu-id="bb692-115">Server app configuration</span></span>

<span data-ttu-id="bb692-116">以下各节介绍了在包括身份验证支持时对项目的添加。</span><span class="sxs-lookup"><span data-stu-id="bb692-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="bb692-117">Startup 类</span><span class="sxs-lookup"><span data-stu-id="bb692-117">Startup class</span></span>

<span data-ttu-id="bb692-118">`Startup`类添加了以下内容：</span><span class="sxs-lookup"><span data-stu-id="bb692-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="bb692-119">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="bb692-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="bb692-120">标识：</span><span class="sxs-lookup"><span data-stu-id="bb692-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="bb692-121">使用另外<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>一种帮助器方法 IdentityServer，用于在 IdentityServer 上设置某些默认 ASP.NET Core 约定：</span><span class="sxs-lookup"><span data-stu-id="bb692-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="bb692-122">使用其他<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>帮助器方法进行身份验证，该方法将应用程序配置为验证 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="bb692-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="bb692-123">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="bb692-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="bb692-124">负责验证请求凭据并在请求上下文上设置用户的身份验证中间件：</span><span class="sxs-lookup"><span data-stu-id="bb692-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="bb692-125">公开 Open ID Connect （OIDC）终结点的 IdentityServer 中间件：</span><span class="sxs-lookup"><span data-stu-id="bb692-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="bb692-126">身份验证和授权中间件：</span><span class="sxs-lookup"><span data-stu-id="bb692-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="bb692-127">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="bb692-127">AddApiAuthorization</span></span>

<span data-ttu-id="bb692-128"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Helper 方法为 ASP.NET Core 情况配置[IdentityServer](https://identityserver.io/) 。</span><span class="sxs-lookup"><span data-stu-id="bb692-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="bb692-129">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="bb692-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="bb692-130">在最常见的情况下，IdentityServer 会造成不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="bb692-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="bb692-131">因此，我们考虑到了一个很好的起点。</span><span class="sxs-lookup"><span data-stu-id="bb692-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="bb692-132">身份验证需要更改后，IdentityServer 的全部功能仍可用于自定义身份验证，以满足应用程序的要求。</span><span class="sxs-lookup"><span data-stu-id="bb692-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="bb692-133">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="bb692-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="bb692-134"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Helper 方法为应用配置策略方案作为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="bb692-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="bb692-135">此策略配置为允许标识处理路由到标识 URL 空间`/Identity`中任何子路径的所有请求。</span><span class="sxs-lookup"><span data-stu-id="bb692-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="bb692-136"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="bb692-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="bb692-137">此外，此方法：</span><span class="sxs-lookup"><span data-stu-id="bb692-137">Additionally, this method:</span></span>

* <span data-ttu-id="bb692-138">使用 IdentityServer `{APPLICATION NAME}API`的默认范围注册 API 资源`{APPLICATION NAME}API`。</span><span class="sxs-lookup"><span data-stu-id="bb692-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="bb692-139">将 JWT 持有者令牌中间件配置为验证 IdentityServer 为应用程序颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="bb692-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="bb692-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="bb692-140">WeatherForecastController</span></span>

<span data-ttu-id="bb692-141">在`WeatherForecastController` （*控制器/WeatherForecastController*）中， [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)特性应用于类。</span><span class="sxs-lookup"><span data-stu-id="bb692-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="bb692-142">属性指示用户必须根据默认策略进行授权才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="bb692-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="bb692-143">默认授权策略配置为使用默认身份验证方案，该方案由<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>设置为之前提到的策略方案。</span><span class="sxs-lookup"><span data-stu-id="bb692-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="bb692-144">Helper 方法将配置<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>为应用程序请求的默认处理程序。</span><span class="sxs-lookup"><span data-stu-id="bb692-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="bb692-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="bb692-145">ApplicationDbContext</span></span>

<span data-ttu-id="bb692-146">在`ApplicationDbContext` （*Data/ApplicationDbContext*）中，在标识中使用<xref:Microsoft.EntityFrameworkCore.DbContext>相同的，它扩展<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>为包含 IdentityServer 的架构。</span><span class="sxs-lookup"><span data-stu-id="bb692-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="bb692-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 派生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。</span><span class="sxs-lookup"><span data-stu-id="bb692-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="bb692-148">若要完全控制数据库架构，请从可用的标识<xref:Microsoft.EntityFrameworkCore.DbContext>类中继承一个，并通过在`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating`方法中调用来配置上下文，使其包含标识架构。</span><span class="sxs-lookup"><span data-stu-id="bb692-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="bb692-149">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="bb692-149">OidcConfigurationController</span></span>

<span data-ttu-id="bb692-150">在`OidcConfigurationController` （controller */OidcConfigurationController*）中，客户端终结点预配为提供 OIDC 参数。</span><span class="sxs-lookup"><span data-stu-id="bb692-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="bb692-151">应用设置文件</span><span class="sxs-lookup"><span data-stu-id="bb692-151">App settings files</span></span>

<span data-ttu-id="bb692-152">在项目根目录下的应用设置文件（*appsettings*）中， `IdentityServer`部分描述了已配置的客户端的列表。</span><span class="sxs-lookup"><span data-stu-id="bb692-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="bb692-153">在下面的示例中，有一个客户端。</span><span class="sxs-lookup"><span data-stu-id="bb692-153">In the following example, there's a single client.</span></span> <span data-ttu-id="bb692-154">客户端名称对应于应用名称，并按约定映射到 OAuth `ClientId`参数。</span><span class="sxs-lookup"><span data-stu-id="bb692-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="bb692-155">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="bb692-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="bb692-156">此配置文件可在内部使用，以促进简化服务器配置过程的约定。</span><span class="sxs-lookup"><span data-stu-id="bb692-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="bb692-157">客户端应用配置</span><span class="sxs-lookup"><span data-stu-id="bb692-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="bb692-158">身份验证包</span><span class="sxs-lookup"><span data-stu-id="bb692-158">Authentication package</span></span>

<span data-ttu-id="bb692-159">当创建应用以使用单个用户帐户（`Individual`）时，应用会在应用的项目文件中自动接收`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包的包引用。</span><span class="sxs-lookup"><span data-stu-id="bb692-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="bb692-160">包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。</span><span class="sxs-lookup"><span data-stu-id="bb692-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bb692-161">如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="bb692-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="bb692-162">将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。</span><span class="sxs-lookup"><span data-stu-id="bb692-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="bb692-163">API 授权支持</span><span class="sxs-lookup"><span data-stu-id="bb692-163">API authorization support</span></span>

<span data-ttu-id="bb692-164">通过`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包内提供的扩展方法将对用户进行身份验证的支持插入到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="bb692-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="bb692-165">此方法设置应用与现有授权系统交互所需的所有服务。</span><span class="sxs-lookup"><span data-stu-id="bb692-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="bb692-166">默认情况下，它会按中`_configuration/{client-id}`的惯例加载应用的配置。</span><span class="sxs-lookup"><span data-stu-id="bb692-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="bb692-167">按照约定，将客户端 ID 设置为应用的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="bb692-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="bb692-168">可以通过使用选项调用重载，将此 URL 更改为指向不同的终结点。</span><span class="sxs-lookup"><span data-stu-id="bb692-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="bb692-169">导入文件</span><span class="sxs-lookup"><span data-stu-id="bb692-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="bb692-170">索引页面</span><span class="sxs-lookup"><span data-stu-id="bb692-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="bb692-171">应用组件</span><span class="sxs-lookup"><span data-stu-id="bb692-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="bb692-172">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="bb692-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="bb692-173">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="bb692-173">LoginDisplay component</span></span>

<span data-ttu-id="bb692-174">组件（*shared/LoginDisplay* `MainLayout` ）在组件（*shared/MainLayout*）中呈现并管理以下行为： `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="bb692-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="bb692-175">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="bb692-175">For authenticated users:</span></span>
  * <span data-ttu-id="bb692-176">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="bb692-176">Displays the current user name.</span></span>
  * <span data-ttu-id="bb692-177">提供指向 ASP.NET Core 标识中的用户配置文件页的链接。</span><span class="sxs-lookup"><span data-stu-id="bb692-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="bb692-178">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="bb692-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="bb692-179">对于匿名用户：</span><span class="sxs-lookup"><span data-stu-id="bb692-179">For anonymous users:</span></span>
  * <span data-ttu-id="bb692-180">提供注册的选项。</span><span class="sxs-lookup"><span data-stu-id="bb692-180">Offers the option to register.</span></span>
  * <span data-ttu-id="bb692-181">提供用于登录的选项。</span><span class="sxs-lookup"><span data-stu-id="bb692-181">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a><span data-ttu-id="bb692-182">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="bb692-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="bb692-183">FetchData 组件</span><span class="sxs-lookup"><span data-stu-id="bb692-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="bb692-184">运行应用</span><span class="sxs-lookup"><span data-stu-id="bb692-184">Run the app</span></span>

<span data-ttu-id="bb692-185">从服务器项目运行应用。</span><span class="sxs-lookup"><span data-stu-id="bb692-185">Run the app from the Server project.</span></span> <span data-ttu-id="bb692-186">使用 Visual Studio 时，请在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="bb692-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bb692-187">其他资源</span><span class="sxs-lookup"><span data-stu-id="bb692-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
