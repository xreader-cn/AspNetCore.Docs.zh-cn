---
title: ASP.NET Core 上的单页面应用的身份验证简介
author: javiercn
description: '用于 Identity ASP.NET Core 应用内托管的单页面应用。'
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051975"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="77758-103">Spa 的身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="77758-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="77758-104">ASP.NET Core 3.1 和更高版本模板在单页面应用中提供身份验证， (Spa) 使用对 API 授权的支持。</span><span class="sxs-lookup"><span data-stu-id="77758-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="77758-105">ASP.NET Core Identity对于身份验证和存储用户，将与用于实现 OpenID Connect 的[ Identity 服务器](https://identityserver.io/)结合。</span><span class="sxs-lookup"><span data-stu-id="77758-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="77758-106">已将身份验证参数添加到 " **角度** " 和 " **响应** " 项目模板，该模板类似于 Web 应用程序中的身份验证参数 **(模型-视图-控制器)** (MVC) 和 **web 应用** (Razor 页) 项目模板。</span><span class="sxs-lookup"><span data-stu-id="77758-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="77758-107">允许的参数值为 **None** 和 **个体** 。</span><span class="sxs-lookup"><span data-stu-id="77758-107">The allowed parameter values are **None** and **Individual** .</span></span> <span data-ttu-id="77758-108">**React.js 和 Redux** 项目模板此时不支持身份验证参数。</span><span class="sxs-lookup"><span data-stu-id="77758-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="77758-109">使用 API 授权支持创建应用</span><span class="sxs-lookup"><span data-stu-id="77758-109">Create an app with API authorization support</span></span>

<span data-ttu-id="77758-110">用户身份验证和授权可用于角度和响应 Spa。</span><span class="sxs-lookup"><span data-stu-id="77758-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="77758-111">打开命令 shell，并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="77758-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="77758-112">**角** ：</span><span class="sxs-lookup"><span data-stu-id="77758-112">**Angular** :</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="77758-113">**响应** ：</span><span class="sxs-lookup"><span data-stu-id="77758-113">**React** :</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="77758-114">上述命令创建一个 ASP.NET Core 应用，其中包含包含 SPA 的 *ClientApp* 目录。</span><span class="sxs-lookup"><span data-stu-id="77758-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="77758-115">应用的 ASP.NET Core 组件的一般描述</span><span class="sxs-lookup"><span data-stu-id="77758-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="77758-116">以下各节介绍了在包括身份验证支持的情况下对项目添加的内容：</span><span class="sxs-lookup"><span data-stu-id="77758-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="77758-117">Startup 类</span><span class="sxs-lookup"><span data-stu-id="77758-117">Startup class</span></span>

<span data-ttu-id="77758-118">下面的代码示例依赖于 [AspNetCore. ApiAuthorization。 IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="77758-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="77758-119">示例使用 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 和扩展方法配置 API 身份验证和授权 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> 。</span><span class="sxs-lookup"><span data-stu-id="77758-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="77758-120">使用带有身份验证的响应或角 SPA 项目模板的项目包括对此包的引用。</span><span class="sxs-lookup"><span data-stu-id="77758-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="77758-121">`Startup`类添加了以下内容：</span><span class="sxs-lookup"><span data-stu-id="77758-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="77758-122">在 `Startup.ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="77758-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="77758-123">Identity 对于默认的 UI：</span><span class="sxs-lookup"><span data-stu-id="77758-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="77758-124">Identity具有其他 `AddApiAuthorization` 帮助器方法的服务器，用于在服务器上设置某些默认 ASP.NET Core 约定 Identity ：</span><span class="sxs-lookup"><span data-stu-id="77758-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="77758-125">包含附加 `AddIdentityServerJwt` 帮助器方法的身份验证，该方法将应用配置为验证由 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="77758-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="77758-126">在 `Startup.Configure` 方法中：</span><span class="sxs-lookup"><span data-stu-id="77758-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="77758-127">负责验证请求凭据并在请求上下文上设置用户的身份验证中间件：</span><span class="sxs-lookup"><span data-stu-id="77758-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="77758-128">Identity公开 OpenID connect 终结点的服务器中间件：</span><span class="sxs-lookup"><span data-stu-id="77758-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="77758-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="77758-129">AddApiAuthorization</span></span>

<span data-ttu-id="77758-130">此帮助程序方法将 Identity 服务器配置为使用受支持的配置。</span><span class="sxs-lookup"><span data-stu-id="77758-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="77758-131">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="77758-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="77758-132">同时，对于最常见的方案，这会造成不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="77758-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="77758-133">因此，系统会为您提供一组约定和配置选项，这是一个很好的起点。</span><span class="sxs-lookup"><span data-stu-id="77758-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="77758-134">一旦你的身份验证需要更改，服务器的全部功能 Identity 仍可用于自定义身份验证以满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="77758-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="77758-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="77758-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="77758-136">此帮助器方法将应用程序的策略方案配置为默认的身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="77758-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="77758-137">此策略配置为允许 Identity 处理路由到 Identity URL 空间 "/" 中任何子路径的所有请求 Identity 。</span><span class="sxs-lookup"><span data-stu-id="77758-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="77758-138">`JwtBearerHandler` 处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="77758-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="77758-139">此外，此方法向服务器注册一个 `<<ApplicationName>>API` Identity 具有默认作用域的 API 资源 `<<ApplicationName>>API` ，并将 JWT 持有者令牌中间件配置为验证 Identity 服务器为应用程序颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="77758-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="77758-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="77758-140">WeatherForecastController</span></span>

<span data-ttu-id="77758-141">在 *Controllers\WeatherForecastController.cs* 文件中，请注意 `[Authorize]` 应用于类的属性，该属性指示用户需要根据默认策略进行授权才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="77758-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="77758-142">默认授权策略将配置为使用默认的身份验证方案，该方案由设置 `AddIdentityServerJwt` 为上面提到的策略方案，并使此 `JwtBearerHandler` 类 helper 方法配置的应用程序请求的默认处理程序。</span><span class="sxs-lookup"><span data-stu-id="77758-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="77758-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="77758-143">ApplicationDbContext</span></span>

<span data-ttu-id="77758-144">在 *Data\ApplicationDbContext.cs* 文件中，请注意， `DbContext` 在中使用了相同的， Identity 因为它将扩展 `ApiAuthorizationDbContext` (一个派生程度更高的类 `IdentityDbContext`) 以包括服务器的架构 Identity 。</span><span class="sxs-lookup"><span data-stu-id="77758-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="77758-145">若要完全控制数据库架构，请从某个可用的类继承， Identity `DbContext` 并将上下文配置为 Identity 通过调用方法来包含架构 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="77758-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="77758-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="77758-146">OidcConfigurationController</span></span>

<span data-ttu-id="77758-147">在 *Controllers\OidcConfigurationController.cs* 文件中，请注意为提供客户端需要使用的 OIDC 参数而设置的终结点。</span><span class="sxs-lookup"><span data-stu-id="77758-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="77758-148">在 *appsettings.json* 项目根目录的文件中，有一个新 `IdentityServer` 部分描述了已配置的客户端的列表。</span><span class="sxs-lookup"><span data-stu-id="77758-148">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="77758-149">下例中存在一个客户端。</span><span class="sxs-lookup"><span data-stu-id="77758-149">In the following example, there's a single client.</span></span> <span data-ttu-id="77758-150">客户端名称对应于应用名称，并通过约定映射到 OAuth `ClientId` 参数。</span><span class="sxs-lookup"><span data-stu-id="77758-150">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="77758-151">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="77758-151">The profile indicates the app type being configured.</span></span> <span data-ttu-id="77758-152">它在内部用于驱动约定，以简化服务器的配置过程。</span><span class="sxs-lookup"><span data-stu-id="77758-152">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="77758-153">有几个配置文件可用，如 " [应用程序配置文件](#application-profiles) " 部分中所述。</span><span class="sxs-lookup"><span data-stu-id="77758-153">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="77758-154">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="77758-154">appsettings.Development.json</span></span>

<span data-ttu-id="77758-155">在项目根的 *appsettings.Development.js* 文件中，有一个 `IdentityServer` 描述用于对令牌进行签名的密钥的部分。</span><span class="sxs-lookup"><span data-stu-id="77758-155">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="77758-156">部署到生产环境时，需要在应用中预配和部署密钥，如 " [部署到生产](#deploy-to-production) " 一节中所述。</span><span class="sxs-lookup"><span data-stu-id="77758-156">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="77758-157">角应用的一般说明</span><span class="sxs-lookup"><span data-stu-id="77758-157">General description of the Angular app</span></span>

<span data-ttu-id="77758-158">角度模板中的身份验证和 API 授权支持位于其自身的 *ClientApp\src\api-authorization* 目录中。</span><span class="sxs-lookup"><span data-stu-id="77758-158">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="77758-159">模块由以下元素组成：</span><span class="sxs-lookup"><span data-stu-id="77758-159">The module is composed of the following elements:</span></span>

* <span data-ttu-id="77758-160">3个组件：</span><span class="sxs-lookup"><span data-stu-id="77758-160">3 components:</span></span>
  * <span data-ttu-id="77758-161">*login. ts* ：处理应用的登录流。</span><span class="sxs-lookup"><span data-stu-id="77758-161">*login.component.ts* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="77758-162">node.js：处理应用程序的注销 *流。*</span><span class="sxs-lookup"><span data-stu-id="77758-162">*logout.component.ts* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="77758-163">*login-menu. ts* ：一个小组件，显示以下一组链接：</span><span class="sxs-lookup"><span data-stu-id="77758-163">*login-menu.component.ts* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="77758-164">用户进行身份验证时，用户配置文件管理和注销链接。</span><span class="sxs-lookup"><span data-stu-id="77758-164">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="77758-165">用户未通过身份验证时的注册和登录链接。</span><span class="sxs-lookup"><span data-stu-id="77758-165">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="77758-166">`AuthorizeGuard`可以添加到路由中的路由防护，要求先对用户进行身份验证，然后才能访问路由。</span><span class="sxs-lookup"><span data-stu-id="77758-166">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="77758-167">`AuthorizeInterceptor`向用户进行身份验证时，将访问令牌附加到针对 API 的传出 HTTP 请求的 http 侦听器。</span><span class="sxs-lookup"><span data-stu-id="77758-167">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="77758-168">一 `AuthorizeService` 项服务，用于处理身份验证过程的较低级别详细信息，并向应用程序的其余部分提供有关使用情况的已通过身份验证的用户的信息。</span><span class="sxs-lookup"><span data-stu-id="77758-168">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="77758-169">用于定义与应用的身份验证部分关联的路由的角模块。</span><span class="sxs-lookup"><span data-stu-id="77758-169">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="77758-170">它公开登录菜单组件、侦听器、防护和服务，以便从应用程序的其余部分使用。</span><span class="sxs-lookup"><span data-stu-id="77758-170">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="77758-171">响应应用的一般说明</span><span class="sxs-lookup"><span data-stu-id="77758-171">General description of the React app</span></span>

<span data-ttu-id="77758-172">响应模板中的身份验证和 API 授权支持位于 *ClientApp\src\components\api-authorization* 目录中。</span><span class="sxs-lookup"><span data-stu-id="77758-172">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="77758-173">它由以下元素组成：</span><span class="sxs-lookup"><span data-stu-id="77758-173">It's composed of the following elements:</span></span>

* <span data-ttu-id="77758-174">4个组件：</span><span class="sxs-lookup"><span data-stu-id="77758-174">4 components:</span></span>
  * <span data-ttu-id="77758-175">*Login.js* ：处理应用的登录流。</span><span class="sxs-lookup"><span data-stu-id="77758-175">*Login.js* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="77758-176">*Logout.js* ：处理应用程序的注销流。</span><span class="sxs-lookup"><span data-stu-id="77758-176">*Logout.js* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="77758-177">*LoginMenu.js* ：显示以下链接集之一的小组件：</span><span class="sxs-lookup"><span data-stu-id="77758-177">*LoginMenu.js* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="77758-178">用户进行身份验证时，用户配置文件管理和注销链接。</span><span class="sxs-lookup"><span data-stu-id="77758-178">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="77758-179">用户未通过身份验证时的注册和登录链接。</span><span class="sxs-lookup"><span data-stu-id="77758-179">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="77758-180">*AuthorizeRoute.js* ：路由组件，需要先对用户进行身份验证，然后才能呈现参数中指示的组件 `Component` 。</span><span class="sxs-lookup"><span data-stu-id="77758-180">*AuthorizeRoute.js* : A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="77758-181">`authService`类的导出实例 `AuthorizeService` ，用于处理身份验证过程的较低级别细节，并向应用程序的其余部分提供有关使用情况的已通过身份验证的用户的信息。</span><span class="sxs-lookup"><span data-stu-id="77758-181">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="77758-182">现在，你已了解解决方案的主要组件，可以更深入地了解应用程序的各个方案。</span><span class="sxs-lookup"><span data-stu-id="77758-182">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="77758-183">要求对新 API 进行授权</span><span class="sxs-lookup"><span data-stu-id="77758-183">Require authorization on a new API</span></span>

<span data-ttu-id="77758-184">默认情况下，系统配置为轻松地要求对新 Api 进行授权。</span><span class="sxs-lookup"><span data-stu-id="77758-184">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="77758-185">为此，请创建新的控制器，将 `[Authorize]` 属性添加到控制器类或控制器中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="77758-185">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="77758-186">自定义 API 身份验证处理程序</span><span class="sxs-lookup"><span data-stu-id="77758-186">Customize the API authentication handler</span></span>

<span data-ttu-id="77758-187">若要自定义 API 的 JWT 处理程序的配置，请配置其 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 实例：</span><span class="sxs-lookup"><span data-stu-id="77758-187">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="77758-188">API 的 JWT 处理程序会引发事件，这些事件可以使用来控制身份验证过程 `JwtBearerEvents` 。</span><span class="sxs-lookup"><span data-stu-id="77758-188">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="77758-189">若要为 API 授权提供支持，请 `AddIdentityServerJwt` 注册其自己的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="77758-189">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="77758-190">若要自定义事件的处理，请根据需要使用其他逻辑来包装现有的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="77758-190">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="77758-191">例如： 。</span><span class="sxs-lookup"><span data-stu-id="77758-191">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="77758-192">在前面的代码中， `OnTokenValidated` 事件处理程序将替换为自定义实现。</span><span class="sxs-lookup"><span data-stu-id="77758-192">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="77758-193">此实现：</span><span class="sxs-lookup"><span data-stu-id="77758-193">This implementation:</span></span>

1. <span data-ttu-id="77758-194">调用 API 授权支持提供的原始实现。</span><span class="sxs-lookup"><span data-stu-id="77758-194">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="77758-195">运行自己的自定义逻辑。</span><span class="sxs-lookup"><span data-stu-id="77758-195">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="77758-196">保护客户端路由 (角度) </span><span class="sxs-lookup"><span data-stu-id="77758-196">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="77758-197">通过将授权防护添加到在配置路由时要运行的防护列表，来保护客户端路由。</span><span class="sxs-lookup"><span data-stu-id="77758-197">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="77758-198">例如，可以在 `fetch-data` 主应用角模块内查看路由的配置方式：</span><span class="sxs-lookup"><span data-stu-id="77758-198">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="77758-199">需要说明的是，保护路由不会保护实际端点 (仍需要 `[Authorize]` 应用属性) 但它只会阻止用户在未通过身份验证时导航到给定的客户端路由。</span><span class="sxs-lookup"><span data-stu-id="77758-199">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="77758-200"> (角度) 对 API 请求进行身份验证</span><span class="sxs-lookup"><span data-stu-id="77758-200">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="77758-201">对与应用程序一起托管的 Api 的请求进行身份验证是通过使用应用定义的 HTTP 客户端侦听器来自动完成的。</span><span class="sxs-lookup"><span data-stu-id="77758-201">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="77758-202">保护客户端路由 (响应) </span><span class="sxs-lookup"><span data-stu-id="77758-202">Protect a client-side route (React)</span></span>

<span data-ttu-id="77758-203">使用 `AuthorizeRoute` 组件而不是普通组件来保护客户端路由 `Route` 。</span><span class="sxs-lookup"><span data-stu-id="77758-203">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="77758-204">例如，请注意该 `fetch-data` 路由在组件中的配置方式 `App` ：</span><span class="sxs-lookup"><span data-stu-id="77758-204">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="77758-205">保护路由：</span><span class="sxs-lookup"><span data-stu-id="77758-205">Protecting a route:</span></span>

* <span data-ttu-id="77758-206">不保护实际端点 (仍需要将 `[Authorize]` 属性应用于) 。</span><span class="sxs-lookup"><span data-stu-id="77758-206">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="77758-207">仅阻止用户在未通过身份验证时导航到给定的客户端路由。</span><span class="sxs-lookup"><span data-stu-id="77758-207">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="77758-208">对 API 请求进行身份验证 (响应) </span><span class="sxs-lookup"><span data-stu-id="77758-208">Authenticate API requests (React)</span></span>

<span data-ttu-id="77758-209">通过从中导入实例，对具有反应的请求进行身份验证 `authService` `AuthorizeService` 。</span><span class="sxs-lookup"><span data-stu-id="77758-209">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="77758-210">访问令牌从检索 `authService` ，并附加到请求，如下所示。</span><span class="sxs-lookup"><span data-stu-id="77758-210">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="77758-211">在做出反应的组件中，这种工作通常是在生命周期方法中完成的， `componentDidMount` 或者是来自某些用户交互的结果。</span><span class="sxs-lookup"><span data-stu-id="77758-211">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="77758-212">将 authService 导入组件</span><span class="sxs-lookup"><span data-stu-id="77758-212">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="77758-213">检索访问令牌并将其附加到响应</span><span class="sxs-lookup"><span data-stu-id="77758-213">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="77758-214">部署到生产</span><span class="sxs-lookup"><span data-stu-id="77758-214">Deploy to production</span></span>

<span data-ttu-id="77758-215">若要将应用部署到生产环境，需预配以下资源：</span><span class="sxs-lookup"><span data-stu-id="77758-215">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="77758-216">用于存储 Identity 用户帐户和服务器授予的数据库 Identity 。</span><span class="sxs-lookup"><span data-stu-id="77758-216">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="77758-217">用于对令牌进行签名的生产证书。</span><span class="sxs-lookup"><span data-stu-id="77758-217">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="77758-218">此证书没有特定要求;此证书可以是自签名证书，也可以是通过 CA 颁发机构预配的证书。</span><span class="sxs-lookup"><span data-stu-id="77758-218">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="77758-219">它可通过 PowerShell 或 OpenSSL 等标准工具生成。</span><span class="sxs-lookup"><span data-stu-id="77758-219">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="77758-220">可以将其安装到目标计算机上的证书存储中，或部署为带有强密码的 *.pfx* 文件。</span><span class="sxs-lookup"><span data-stu-id="77758-220">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="77758-221">示例：部署到非 Azure web 托管提供程序</span><span class="sxs-lookup"><span data-stu-id="77758-221">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="77758-222">在 web 托管面板中，创建或加载证书。</span><span class="sxs-lookup"><span data-stu-id="77758-222">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="77758-223">然后，在应用的 *appsettings.json* 文件中修改 `IdentityServer` 部分以包含关键详细信息。</span><span class="sxs-lookup"><span data-stu-id="77758-223">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="77758-224">例如： 。</span><span class="sxs-lookup"><span data-stu-id="77758-224">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="77758-225">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="77758-225">In the preceding example:</span></span>

* <span data-ttu-id="77758-226">`StoreName` 表示存储证书的证书存储区的名称。</span><span class="sxs-lookup"><span data-stu-id="77758-226">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="77758-227">在这种情况下，它指向 web 宿主存储区。</span><span class="sxs-lookup"><span data-stu-id="77758-227">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="77758-228">`StoreLocation` 表示 `CurrentUser` 在这种情况下从 (中加载证书的位置) 。</span><span class="sxs-lookup"><span data-stu-id="77758-228">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="77758-229">`Name` 对应于证书的可分辨主题。</span><span class="sxs-lookup"><span data-stu-id="77758-229">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="77758-230">示例：部署到 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="77758-230">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="77758-231">本部分介绍如何使用存储在证书存储区中的证书，将应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="77758-231">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="77758-232">若要将应用修改为从证书存储区中加载证书，请在稍后的步骤中配置 Azure 门户应用时，需要使用标准层服务计划或更高版本。</span><span class="sxs-lookup"><span data-stu-id="77758-232">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="77758-233">在应用的 *appsettings.json* 文件中，修改 `IdentityServer` 部分以包含关键详细信息：</span><span class="sxs-lookup"><span data-stu-id="77758-233">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="77758-234">存储名称表示存储证书的证书存储区的名称。</span><span class="sxs-lookup"><span data-stu-id="77758-234">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="77758-235">在这种情况下，它指向个人用户存储区。</span><span class="sxs-lookup"><span data-stu-id="77758-235">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="77758-236">应用商店位置表示从 (或) 中加载证书的位置 `CurrentUser` `LocalMachine` 。</span><span class="sxs-lookup"><span data-stu-id="77758-236">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="77758-237">证书上的名称属性对应于证书的可分辨主题。</span><span class="sxs-lookup"><span data-stu-id="77758-237">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="77758-238">若要部署到 Azure App Service，请按照将 [应用程序部署到 Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)中的步骤操作，该步骤介绍了如何创建必要的 Azure 资源，以及如何将应用程序部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="77758-238">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="77758-239">按照前面的说明操作后，应用程序将部署到 Azure，但尚不起作用。</span><span class="sxs-lookup"><span data-stu-id="77758-239">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="77758-240">应用使用的证书必须在 Azure 门户中进行配置。</span><span class="sxs-lookup"><span data-stu-id="77758-240">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="77758-241">找到证书的指纹，并按照 [加载证书](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)中所述的步骤进行操作。</span><span class="sxs-lookup"><span data-stu-id="77758-241">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="77758-242">虽然这些步骤提及 SSL，但在 Azure 门户中有一个 " **私有证书** " 部分，你可以在其中上传预配的证书以与应用一起使用。</span><span class="sxs-lookup"><span data-stu-id="77758-242">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="77758-243">在 Azure 门户中配置应用和应用设置后，请在门户中重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="77758-243">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="77758-244">其他配置选项</span><span class="sxs-lookup"><span data-stu-id="77758-244">Other configuration options</span></span>

<span data-ttu-id="77758-245">支持 API 授权构建在 Identity 服务器之上，具有一组约定、默认值和增强功能，可简化 spa 的体验。</span><span class="sxs-lookup"><span data-stu-id="77758-245">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="77758-246">不用说， Identity 如果 ASP.NET Core 集成不涵盖方案，则可以在幕后使用服务器的完整功能。</span><span class="sxs-lookup"><span data-stu-id="77758-246">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="77758-247">ASP.NET Core 支持重点介绍 "第一方" 应用，其中的所有应用都是由我们的组织创建和部署的。</span><span class="sxs-lookup"><span data-stu-id="77758-247">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="77758-248">因此，不支持许可或联合等。</span><span class="sxs-lookup"><span data-stu-id="77758-248">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="77758-249">对于这些方案，请使用 Identity 服务器并按照其文档进行操作。</span><span class="sxs-lookup"><span data-stu-id="77758-249">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="77758-250">应用程序配置文件</span><span class="sxs-lookup"><span data-stu-id="77758-250">Application profiles</span></span>

<span data-ttu-id="77758-251">应用程序配置文件是用于进一步定义其参数的应用的预定义配置。</span><span class="sxs-lookup"><span data-stu-id="77758-251">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="77758-252">目前支持以下配置文件：</span><span class="sxs-lookup"><span data-stu-id="77758-252">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="77758-253">`IdentityServerSPA`：表示 Identity 作为一个单元与服务器一起托管的 SPA。</span><span class="sxs-lookup"><span data-stu-id="77758-253">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="77758-254">`redirect_uri`默认值为 `/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="77758-254">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="77758-255">`post_logout_redirect_uri`默认值为 `/authentication/logout-callback` 。</span><span class="sxs-lookup"><span data-stu-id="77758-255">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="77758-256">作用域集包括 `openid` `profile` 为应用中的 api 定义的、和每个作用域。</span><span class="sxs-lookup"><span data-stu-id="77758-256">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="77758-257">一组允许的 OIDC 响应类型为 `id_token token` ，每个响应类型分别 `id_token` (`token`) 。</span><span class="sxs-lookup"><span data-stu-id="77758-257">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="77758-258">允许的响应模式为 `fragment` 。</span><span class="sxs-lookup"><span data-stu-id="77758-258">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="77758-259">`SPA`：表示不随服务器承载的 SPA Identity 。</span><span class="sxs-lookup"><span data-stu-id="77758-259">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="77758-260">作用域集包括 `openid` `profile` 为应用中的 api 定义的、和每个作用域。</span><span class="sxs-lookup"><span data-stu-id="77758-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="77758-261">一组允许的 OIDC 响应类型为 `id_token token` ，每个响应类型分别 `id_token` (`token`) 。</span><span class="sxs-lookup"><span data-stu-id="77758-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="77758-262">允许的响应模式为 `fragment` 。</span><span class="sxs-lookup"><span data-stu-id="77758-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="77758-263">`IdentityServerJwt`：表示与服务器一起托管的 API Identity 。</span><span class="sxs-lookup"><span data-stu-id="77758-263">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="77758-264">应用配置为具有一个默认为应用名称的作用域。</span><span class="sxs-lookup"><span data-stu-id="77758-264">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="77758-265">`API`：表示不与服务器托管的 API Identity 。</span><span class="sxs-lookup"><span data-stu-id="77758-265">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="77758-266">应用配置为具有一个默认为应用名称的作用域。</span><span class="sxs-lookup"><span data-stu-id="77758-266">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="77758-267">通过 AppSettings 配置</span><span class="sxs-lookup"><span data-stu-id="77758-267">Configuration through AppSettings</span></span>

<span data-ttu-id="77758-268">通过将应用添加到或列表，通过配置系统配置应用 `Clients` `Resources` 。</span><span class="sxs-lookup"><span data-stu-id="77758-268">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="77758-269">配置每个客户端的 `redirect_uri` 和 `post_logout_redirect_uri` 属性，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="77758-269">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="77758-270">配置资源时，可以按如下所示配置资源的作用域：</span><span class="sxs-lookup"><span data-stu-id="77758-270">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="77758-271">通过代码进行配置</span><span class="sxs-lookup"><span data-stu-id="77758-271">Configuration through code</span></span>

<span data-ttu-id="77758-272">你还可以通过代码使用的重载来配置客户端和资源 `AddApiAuthorization` ，该重载采用操作来配置选项。</span><span class="sxs-lookup"><span data-stu-id="77758-272">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="77758-273">其他资源</span><span class="sxs-lookup"><span data-stu-id="77758-273">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
