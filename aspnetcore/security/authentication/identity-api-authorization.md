---
title: ASP.NET Core 上的单页面应用的身份验证简介
author: javiercn
description: 将标识用于在 ASP.NET Core 应用内托管的单页面应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 98df1aa1671c22384252676c56e8cb4a3a0a35eb
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190492"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="542a7-103">Spa 的身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="542a7-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="542a7-104">ASP.NET Core 3.0 或更高版本通过支持 API 授权在单页面应用（Spa）中提供身份验证。</span><span class="sxs-lookup"><span data-stu-id="542a7-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="542a7-105">用于对用户进行身份验证和存储的 ASP.NET Core 标识与用于实现 Open ID Connect 的[IdentityServer](https://identityserver.io/)结合。</span><span class="sxs-lookup"><span data-stu-id="542a7-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="542a7-106">已将身份验证参数添加到 "**角度**" 和 "**响应**" 项目模板，该模板类似于 web 应用程序中的身份验证参数 **（模型-视图-控制器）** （MVC）和**web 应用程序**（Razor Pages）项目模板。</span><span class="sxs-lookup"><span data-stu-id="542a7-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="542a7-107">允许的参数值为**None**和**个体**。</span><span class="sxs-lookup"><span data-stu-id="542a7-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="542a7-108">目前，**反应和 Redux**项目模板不支持 authentication 参数。</span><span class="sxs-lookup"><span data-stu-id="542a7-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="542a7-109">使用 API 授权支持创建应用</span><span class="sxs-lookup"><span data-stu-id="542a7-109">Create an app with API authorization support</span></span>

<span data-ttu-id="542a7-110">用户身份验证和授权可用于角度和响应 Spa。</span><span class="sxs-lookup"><span data-stu-id="542a7-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="542a7-111">打开命令 shell，并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="542a7-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="542a7-112">**角**：</span><span class="sxs-lookup"><span data-stu-id="542a7-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="542a7-113">**响应**：</span><span class="sxs-lookup"><span data-stu-id="542a7-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="542a7-114">上述命令创建一个 ASP.NET Core 应用，其中包含包含 SPA 的*ClientApp*目录。</span><span class="sxs-lookup"><span data-stu-id="542a7-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="542a7-115">应用的 ASP.NET Core 组件的一般描述</span><span class="sxs-lookup"><span data-stu-id="542a7-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="542a7-116">以下各节介绍了在包括身份验证支持的情况下对项目添加的内容：</span><span class="sxs-lookup"><span data-stu-id="542a7-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="542a7-117">Startup 类</span><span class="sxs-lookup"><span data-stu-id="542a7-117">Startup class</span></span>

<span data-ttu-id="542a7-118">`Startup` 类具有以下附加项：</span><span class="sxs-lookup"><span data-stu-id="542a7-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="542a7-119">在 `Startup.ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="542a7-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="542a7-120">具有默认 UI 的标识：</span><span class="sxs-lookup"><span data-stu-id="542a7-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="542a7-121">使用其他 `AddApiAuthorization` 帮助器方法，在 IdentityServer 上设置一些默认的 ASP.NET Core 约定：</span><span class="sxs-lookup"><span data-stu-id="542a7-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="542a7-122">使用附加的 `AddIdentityServerJwt` 帮助器方法进行身份验证，该方法将应用程序配置为验证 IdentityServer 生成的 JWT 令牌：</span><span class="sxs-lookup"><span data-stu-id="542a7-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="542a7-123">在 `Startup.Configure` 方法中：</span><span class="sxs-lookup"><span data-stu-id="542a7-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="542a7-124">负责验证请求凭据并在请求上下文上设置用户的身份验证中间件：</span><span class="sxs-lookup"><span data-stu-id="542a7-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="542a7-125">公开开放 ID 连接终结点的 IdentityServer 中间件：</span><span class="sxs-lookup"><span data-stu-id="542a7-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="542a7-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="542a7-126">AddApiAuthorization</span></span>

<span data-ttu-id="542a7-127">此帮助器方法将 IdentityServer 配置为使用受支持的配置。</span><span class="sxs-lookup"><span data-stu-id="542a7-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="542a7-128">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用安全问题。</span><span class="sxs-lookup"><span data-stu-id="542a7-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="542a7-129">同时，对于最常见的方案，这会造成不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="542a7-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="542a7-130">因此，系统会为您提供一组约定和配置选项，这是一个很好的起点。</span><span class="sxs-lookup"><span data-stu-id="542a7-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="542a7-131">身份验证需要更改后，IdentityServer 的全部功能仍可用于自定义身份验证以满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="542a7-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="542a7-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="542a7-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="542a7-133">此帮助器方法将应用程序的策略方案配置为默认的身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="542a7-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="542a7-134">此策略配置为允许标识处理所有路由到标识 URL 空间 "/Identity" 中的子路径的请求。</span><span class="sxs-lookup"><span data-stu-id="542a7-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="542a7-135">`JwtBearerHandler` 处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="542a7-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="542a7-136">此外，此方法向 IdentityServer 的默认作用 `<<ApplicationName>>API` 域注册 `<<ApplicationName>>API` API 资源，并配置 JWT 持有者令牌中间件来验证 IdentityServer 为应用颁发的令牌。</span><span class="sxs-lookup"><span data-stu-id="542a7-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="542a7-137">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="542a7-137">WeatherForecastController</span></span>

<span data-ttu-id="542a7-138">在*Controllers\WeatherForecastController.cs*文件中，请注意应用于类的 `[Authorize]` 属性，该属性指示用户需要根据默认策略进行授权才能访问资源。</span><span class="sxs-lookup"><span data-stu-id="542a7-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="542a7-139">默认授权策略将配置为使用默认身份验证方案，该方案通过 `AddIdentityServerJwt` 设置为上面提到的策略方案，使此类 helper 方法将 `JwtBearerHandler` 配置为请求的默认处理程序应用程序。</span><span class="sxs-lookup"><span data-stu-id="542a7-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="542a7-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="542a7-140">ApplicationDbContext</span></span>

<span data-ttu-id="542a7-141">在*Data\ApplicationDbContext.cs*文件中，请注意在标识中使用了相同的 `DbContext`，但它会将 `ApiAuthorizationDbContext` （从 `IdentityDbContext`派生的派生类）扩展为包含 IdentityServer 的架构。</span><span class="sxs-lookup"><span data-stu-id="542a7-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="542a7-142">若要完全控制数据库架构，请从某个可用的标识 `DbContext` 类继承，并通过在 `OnModelCreating` 方法上调用 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`，将上下文配置为包含标识架构。</span><span class="sxs-lookup"><span data-stu-id="542a7-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="542a7-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="542a7-143">OidcConfigurationController</span></span>

<span data-ttu-id="542a7-144">在*Controllers\OidcConfigurationController.cs*文件中，请注意为提供客户端需要使用的 OIDC 参数而设置的终结点。</span><span class="sxs-lookup"><span data-stu-id="542a7-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="542a7-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="542a7-145">appsettings.json</span></span>

<span data-ttu-id="542a7-146">在项目根的*appsettings*文件中，有一个新的 `IdentityServer` 节，其中描述了已配置的客户端的列表。</span><span class="sxs-lookup"><span data-stu-id="542a7-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="542a7-147">在下面的示例中，有一个客户端。</span><span class="sxs-lookup"><span data-stu-id="542a7-147">In the following example, there's a single client.</span></span> <span data-ttu-id="542a7-148">客户端名称对应于应用名称，并按约定映射到 OAuth `ClientId` 参数。</span><span class="sxs-lookup"><span data-stu-id="542a7-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="542a7-149">配置文件指示正在配置的应用类型。</span><span class="sxs-lookup"><span data-stu-id="542a7-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="542a7-150">它在内部用于驱动约定，以简化服务器的配置过程。</span><span class="sxs-lookup"><span data-stu-id="542a7-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="542a7-151">有几个配置文件可用，如 "[应用程序配置文件](#application-profiles)" 部分中所述。</span><span class="sxs-lookup"><span data-stu-id="542a7-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="542a7-152">appsettings.开发 json</span><span class="sxs-lookup"><span data-stu-id="542a7-152">appsettings.Development.json</span></span>

<span data-ttu-id="542a7-153">在*appsettings 中。* 项目根的开发 json 文件，有一个 `IdentityServer` 部分描述用于对令牌进行签名的密钥。</span><span class="sxs-lookup"><span data-stu-id="542a7-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="542a7-154">部署到生产环境时，需要在应用中预配和部署密钥，如 "[部署到生产](#deploy-to-production)" 一节中所述。</span><span class="sxs-lookup"><span data-stu-id="542a7-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="542a7-155">角应用的一般说明</span><span class="sxs-lookup"><span data-stu-id="542a7-155">General description of the Angular app</span></span>

<span data-ttu-id="542a7-156">角度模板中的身份验证和 API 授权支持位于其自身的*ClientApp\src\api-authorization*目录中。</span><span class="sxs-lookup"><span data-stu-id="542a7-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="542a7-157">模块由以下元素组成：</span><span class="sxs-lookup"><span data-stu-id="542a7-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="542a7-158">3个组件：</span><span class="sxs-lookup"><span data-stu-id="542a7-158">3 components:</span></span>
  * <span data-ttu-id="542a7-159">*login. ts*：处理应用的登录流。</span><span class="sxs-lookup"><span data-stu-id="542a7-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="542a7-160">node.js：处理应用程序的注销*流。*</span><span class="sxs-lookup"><span data-stu-id="542a7-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="542a7-161">*login-menu. ts*：一个小组件，显示以下一组链接：</span><span class="sxs-lookup"><span data-stu-id="542a7-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="542a7-162">用户进行身份验证时，用户配置文件管理和注销链接。</span><span class="sxs-lookup"><span data-stu-id="542a7-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="542a7-163">用户未通过身份验证时的注册和登录链接。</span><span class="sxs-lookup"><span data-stu-id="542a7-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="542a7-164">路由防护 `AuthorizeGuard` 可以添加到路由，并在访问路由之前要求用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="542a7-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="542a7-165">一个 HTTP 侦听器 `AuthorizeInterceptor`，它在用户进行身份验证时，将访问令牌附加到针对 API 的传出 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="542a7-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="542a7-166">一种服务 `AuthorizeService`，用于处理身份验证过程的较低级别的详细信息，并向应用程序的其余部分提供有关使用情况的经过身份验证的用户的信息。</span><span class="sxs-lookup"><span data-stu-id="542a7-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="542a7-167">用于定义与应用的身份验证部分关联的路由的角模块。</span><span class="sxs-lookup"><span data-stu-id="542a7-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="542a7-168">它公开登录菜单组件、侦听器、防护和服务，以便从应用程序的其余部分使用。</span><span class="sxs-lookup"><span data-stu-id="542a7-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="542a7-169">响应应用的一般说明</span><span class="sxs-lookup"><span data-stu-id="542a7-169">General description of the React app</span></span>

<span data-ttu-id="542a7-170">响应模板中的身份验证和 API 授权支持位于*ClientApp\src\components\api-authorization*目录中。</span><span class="sxs-lookup"><span data-stu-id="542a7-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="542a7-171">它由以下元素组成：</span><span class="sxs-lookup"><span data-stu-id="542a7-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="542a7-172">4个组件：</span><span class="sxs-lookup"><span data-stu-id="542a7-172">4 components:</span></span>
  * <span data-ttu-id="542a7-173">*Login*：处理应用的登录流。</span><span class="sxs-lookup"><span data-stu-id="542a7-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="542a7-174">*Node.js*：处理应用程序的注销流。</span><span class="sxs-lookup"><span data-stu-id="542a7-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="542a7-175">*LoginMenu*：显示以下链接集之一的小组件：</span><span class="sxs-lookup"><span data-stu-id="542a7-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="542a7-176">用户进行身份验证时，用户配置文件管理和注销链接。</span><span class="sxs-lookup"><span data-stu-id="542a7-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="542a7-177">用户未通过身份验证时的注册和登录链接。</span><span class="sxs-lookup"><span data-stu-id="542a7-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="542a7-178">*AuthorizeRoute*：路由组件，需要先对用户进行身份验证，然后才能呈现 `Component` 参数中指示的组件。</span><span class="sxs-lookup"><span data-stu-id="542a7-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="542a7-179">`AuthorizeService` 的已导出 `authService` 类的实例，用于处理身份验证过程的较低级别细节，并向应用程序的其余部分公开有关使用情况的已通过身份验证的用户的信息。</span><span class="sxs-lookup"><span data-stu-id="542a7-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="542a7-180">现在，你已了解解决方案的主要组件，可以更深入地了解应用程序的各个方案。</span><span class="sxs-lookup"><span data-stu-id="542a7-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="542a7-181">要求对新 API 进行授权</span><span class="sxs-lookup"><span data-stu-id="542a7-181">Require authorization on a new API</span></span>

<span data-ttu-id="542a7-182">默认情况下，系统配置为轻松地要求对新 Api 进行授权。</span><span class="sxs-lookup"><span data-stu-id="542a7-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="542a7-183">为此，请创建新的控制器，并将 `[Authorize]` 特性添加到控制器类或控制器中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="542a7-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="542a7-184">自定义 API 身份验证处理程序</span><span class="sxs-lookup"><span data-stu-id="542a7-184">Customize the API authentication handler</span></span>

<span data-ttu-id="542a7-185">若要自定义 API 的 JWT 处理程序的配置，请配置其 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 实例：</span><span class="sxs-lookup"><span data-stu-id="542a7-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.ConfigureOptions<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="542a7-186">保护客户端路线（角度）</span><span class="sxs-lookup"><span data-stu-id="542a7-186">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="542a7-187">通过将授权防护添加到在配置路由时要运行的防护列表，来保护客户端路由。</span><span class="sxs-lookup"><span data-stu-id="542a7-187">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="542a7-188">例如，可以在主应用角模块中查看如何配置 `fetch-data` 路由：</span><span class="sxs-lookup"><span data-stu-id="542a7-188">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="542a7-189">需要说明的是，保护路由不会保护实际终结点（仍需要应用 `[Authorize]` 属性），但它只会阻止用户在未通过身份验证时导航到给定的客户端路由。</span><span class="sxs-lookup"><span data-stu-id="542a7-189">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="542a7-190">对 API 请求进行身份验证（角度）</span><span class="sxs-lookup"><span data-stu-id="542a7-190">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="542a7-191">对与应用程序一起托管的 Api 的请求进行身份验证是通过使用应用定义的 HTTP 客户端侦听器来自动完成的。</span><span class="sxs-lookup"><span data-stu-id="542a7-191">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="542a7-192">保护客户端路由（响应）</span><span class="sxs-lookup"><span data-stu-id="542a7-192">Protect a client-side route (React)</span></span>

<span data-ttu-id="542a7-193">使用 `AuthorizeRoute` 组件而不是纯 `Route` 组件来保护客户端路由。</span><span class="sxs-lookup"><span data-stu-id="542a7-193">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="542a7-194">例如，请注意在 `App` 组件中如何配置 `fetch-data` 路由：</span><span class="sxs-lookup"><span data-stu-id="542a7-194">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="542a7-195">保护路由：</span><span class="sxs-lookup"><span data-stu-id="542a7-195">Protecting a route:</span></span>

* <span data-ttu-id="542a7-196">不保护实际终结点（仍需要应用 `[Authorize]` 特性）。</span><span class="sxs-lookup"><span data-stu-id="542a7-196">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="542a7-197">仅阻止用户在未通过身份验证时导航到给定的客户端路由。</span><span class="sxs-lookup"><span data-stu-id="542a7-197">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="542a7-198">对 API 请求进行身份验证（响应）</span><span class="sxs-lookup"><span data-stu-id="542a7-198">Authenticate API requests (React)</span></span>

<span data-ttu-id="542a7-199">通过首先从 `AuthorizeService`导入 `authService` 实例，对具有反应的请求进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="542a7-199">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="542a7-200">将从 `authService` 检索访问令牌，并将其附加到请求，如下所示。</span><span class="sxs-lookup"><span data-stu-id="542a7-200">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="542a7-201">在响应组件中，这种工作通常是在 `componentDidMount` 生命周期方法中完成的，或者作为某些用户交互的结果。</span><span class="sxs-lookup"><span data-stu-id="542a7-201">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="542a7-202">将 authService 导入组件</span><span class="sxs-lookup"><span data-stu-id="542a7-202">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="542a7-203">检索访问令牌并将其附加到响应</span><span class="sxs-lookup"><span data-stu-id="542a7-203">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="542a7-204">部署到生产环境</span><span class="sxs-lookup"><span data-stu-id="542a7-204">Deploy to production</span></span>

<span data-ttu-id="542a7-205">若要将应用部署到生产环境，需预配以下资源：</span><span class="sxs-lookup"><span data-stu-id="542a7-205">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="542a7-206">用于存储标识用户帐户和 IdentityServer 授予的数据库。</span><span class="sxs-lookup"><span data-stu-id="542a7-206">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="542a7-207">用于对令牌进行签名的生产证书。</span><span class="sxs-lookup"><span data-stu-id="542a7-207">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="542a7-208">此证书没有特定要求;此证书可以是自签名证书，也可以是通过 CA 颁发机构预配的证书。</span><span class="sxs-lookup"><span data-stu-id="542a7-208">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="542a7-209">它可通过 PowerShell 或 OpenSSL 等标准工具生成。</span><span class="sxs-lookup"><span data-stu-id="542a7-209">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="542a7-210">可以将其安装到目标计算机上的证书存储中，或部署为带有强密码的 *.pfx*文件。</span><span class="sxs-lookup"><span data-stu-id="542a7-210">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="542a7-211">示例：部署到 Azure 网站</span><span class="sxs-lookup"><span data-stu-id="542a7-211">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="542a7-212">本部分介绍如何使用证书存储中存储的证书将应用部署到 Azure 网站。</span><span class="sxs-lookup"><span data-stu-id="542a7-212">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="542a7-213">若要修改应用以从证书存储区中加载证书，请在稍后的步骤中配置应用服务计划。</span><span class="sxs-lookup"><span data-stu-id="542a7-213">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="542a7-214">在应用的*appsettings*文件中，修改 `IdentityServer` 部分以包含关键详细信息：</span><span class="sxs-lookup"><span data-stu-id="542a7-214">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="542a7-215">证书上的名称属性对应于证书的可分辨主题。</span><span class="sxs-lookup"><span data-stu-id="542a7-215">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="542a7-216">存储位置表示从何处加载证书（`CurrentUser` 或 `LocalMachine`）。</span><span class="sxs-lookup"><span data-stu-id="542a7-216">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="542a7-217">存储名称表示存储证书的证书存储区的名称。</span><span class="sxs-lookup"><span data-stu-id="542a7-217">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="542a7-218">在这种情况下，它指向个人用户存储区。</span><span class="sxs-lookup"><span data-stu-id="542a7-218">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="542a7-219">若要部署到 Azure 网站，请遵循将[应用程序部署到 azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)中的步骤部署应用，以创建所需的 Azure 资源，并将该应用部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="542a7-219">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="542a7-220">按照前面的说明操作后，应用程序将部署到 Azure，但尚不起作用。</span><span class="sxs-lookup"><span data-stu-id="542a7-220">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="542a7-221">仍需设置应用使用的证书。</span><span class="sxs-lookup"><span data-stu-id="542a7-221">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="542a7-222">找到要使用的证书的指纹，并按照[加载证书](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)中所述的步骤进行操作。</span><span class="sxs-lookup"><span data-stu-id="542a7-222">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="542a7-223">虽然这些步骤提及 SSL，但在门户上有一个 "**私有证书**" 部分，你可以在其中上传要与应用一起使用的预配证书。</span><span class="sxs-lookup"><span data-stu-id="542a7-223">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="542a7-224">完成此步骤后，重新启动应用程序并确保它正常运行。</span><span class="sxs-lookup"><span data-stu-id="542a7-224">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="542a7-225">其他配置选项</span><span class="sxs-lookup"><span data-stu-id="542a7-225">Other configuration options</span></span>

<span data-ttu-id="542a7-226">支持 API 授权构建在 IdentityServer 的基础之上，具有一组约定、默认值和增强功能，可简化 Spa 的体验。</span><span class="sxs-lookup"><span data-stu-id="542a7-226">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="542a7-227">不用说，如果 ASP.NET Core 集成不涵盖方案，则 IdentityServer 的全部功能都可在幕后使用。</span><span class="sxs-lookup"><span data-stu-id="542a7-227">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="542a7-228">ASP.NET Core 支持重点介绍 "第一方" 应用，其中的所有应用都是由我们的组织创建和部署的。</span><span class="sxs-lookup"><span data-stu-id="542a7-228">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="542a7-229">因此，不支持许可或联合等。</span><span class="sxs-lookup"><span data-stu-id="542a7-229">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="542a7-230">对于这些方案，请使用 IdentityServer 并按照其文档进行操作。</span><span class="sxs-lookup"><span data-stu-id="542a7-230">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="542a7-231">应用程序配置文件</span><span class="sxs-lookup"><span data-stu-id="542a7-231">Application profiles</span></span>

<span data-ttu-id="542a7-232">应用程序配置文件是用于进一步定义其参数的应用的预定义配置。</span><span class="sxs-lookup"><span data-stu-id="542a7-232">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="542a7-233">目前支持以下配置文件：</span><span class="sxs-lookup"><span data-stu-id="542a7-233">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="542a7-234">`IdentityServerSPA`：表示与 IdentityServer 一起托管的 SPA 作为单个单元。</span><span class="sxs-lookup"><span data-stu-id="542a7-234">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="542a7-235">`redirect_uri` 默认为 `/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="542a7-235">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="542a7-236">`post_logout_redirect_uri` 默认为 `/authentication/logout-callback`。</span><span class="sxs-lookup"><span data-stu-id="542a7-236">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="542a7-237">作用域集包括为应用中的 Api 定义的 `openid`、`profile`和每个作用域。</span><span class="sxs-lookup"><span data-stu-id="542a7-237">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="542a7-238">允许的 OIDC 响应类型集为单独 `id_token token` 或每个响应类型（`id_token``token`）。</span><span class="sxs-lookup"><span data-stu-id="542a7-238">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="542a7-239">允许的响应模式为 `fragment`。</span><span class="sxs-lookup"><span data-stu-id="542a7-239">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="542a7-240">`SPA`：表示不与 IdentityServer 托管的 SPA。</span><span class="sxs-lookup"><span data-stu-id="542a7-240">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="542a7-241">作用域集包括为应用中的 Api 定义的 `openid`、`profile`和每个作用域。</span><span class="sxs-lookup"><span data-stu-id="542a7-241">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="542a7-242">允许的 OIDC 响应类型集为单独 `id_token token` 或每个响应类型（`id_token``token`）。</span><span class="sxs-lookup"><span data-stu-id="542a7-242">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="542a7-243">允许的响应模式为 `fragment`。</span><span class="sxs-lookup"><span data-stu-id="542a7-243">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="542a7-244">`IdentityServerJwt`：表示与 IdentityServer 一起托管的 API。</span><span class="sxs-lookup"><span data-stu-id="542a7-244">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="542a7-245">应用配置为具有一个默认为应用名称的作用域。</span><span class="sxs-lookup"><span data-stu-id="542a7-245">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="542a7-246">`API`：表示不与 IdentityServer 托管的 API。</span><span class="sxs-lookup"><span data-stu-id="542a7-246">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="542a7-247">应用配置为具有一个默认为应用名称的作用域。</span><span class="sxs-lookup"><span data-stu-id="542a7-247">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="542a7-248">通过 AppSettings 配置</span><span class="sxs-lookup"><span data-stu-id="542a7-248">Configuration through AppSettings</span></span>

<span data-ttu-id="542a7-249">通过配置系统将应用添加到 `Clients` 或 `Resources`的列表来配置这些应用。</span><span class="sxs-lookup"><span data-stu-id="542a7-249">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="542a7-250">配置每个客户端的 `redirect_uri` 和 `post_logout_redirect_uri` 属性，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="542a7-250">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="542a7-251">配置资源时，可以按如下所示配置资源的作用域：</span><span class="sxs-lookup"><span data-stu-id="542a7-251">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="542a7-252">通过代码进行配置</span><span class="sxs-lookup"><span data-stu-id="542a7-252">Configuration through code</span></span>

<span data-ttu-id="542a7-253">你还可以通过代码使用采用操作来配置选项的 `AddApiAuthorization` 的重载来配置客户端和资源。</span><span class="sxs-lookup"><span data-stu-id="542a7-253">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="542a7-254">其他资源</span><span class="sxs-lookup"><span data-stu-id="542a7-254">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
