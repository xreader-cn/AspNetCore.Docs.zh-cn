---
title: 在 ASP.NET Core 上的单一页面应用的身份验证简介
author: javiercn
description: 使用与托管 ASP.NET Core 应用在单页面应用程序的标识。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: 302a5e10a70e40e75ab9fe4b3e5a98c4e847b822
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815220"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="37e3c-103">身份验证和授权 Spa</span><span class="sxs-lookup"><span data-stu-id="37e3c-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="37e3c-104">ASP.NET Core 3.0 或更高版本提供了单一页面应用 (Spa) 中的身份验证 API 授权使用的支持。</span><span class="sxs-lookup"><span data-stu-id="37e3c-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="37e3c-105">ASP.NET Core 标识进行身份验证和存储用户结合[IdentityServer](https://identityserver.io/)实现 Open ID Connect。</span><span class="sxs-lookup"><span data-stu-id="37e3c-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="37e3c-106">身份验证参数已添加到**Angular**并**做出反应**项目模板类似于中的身份验证参数**Web 应用程序 （模型-视图-控制器）** (MVC) 和**Web 应用程序**（Razor 页面） 项目模板。</span><span class="sxs-lookup"><span data-stu-id="37e3c-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="37e3c-107">允许的参数值为**无**并**单个**。</span><span class="sxs-lookup"><span data-stu-id="37e3c-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="37e3c-108">**React.js 和 Redux**项目模板不支持这一次身份验证参数。</span><span class="sxs-lookup"><span data-stu-id="37e3c-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="37e3c-109">API 身份验证支持使用创建应用</span><span class="sxs-lookup"><span data-stu-id="37e3c-109">Create an app with API authorization support</span></span>

<span data-ttu-id="37e3c-110">通过 Angular 和 React Spa，可以使用用户身份验证和授权。</span><span class="sxs-lookup"><span data-stu-id="37e3c-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="37e3c-111">打开命令外壳，并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="37e3c-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="37e3c-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="37e3c-112">**Angular**:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="37e3c-113">**React**:</span><span class="sxs-lookup"><span data-stu-id="37e3c-113">**React**:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="37e3c-114">上述命令创建与 ASP.NET Core 应用*ClientApp*目录，其中包含 SPA。</span><span class="sxs-lookup"><span data-stu-id="37e3c-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="37e3c-115">应用程序的 ASP.NET Core 组件的一般说明</span><span class="sxs-lookup"><span data-stu-id="37e3c-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="37e3c-116">以下部分介绍向项目添加内容时，则包括身份验证支持：</span><span class="sxs-lookup"><span data-stu-id="37e3c-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="37e3c-117">启动类</span><span class="sxs-lookup"><span data-stu-id="37e3c-117">Startup class</span></span>

<span data-ttu-id="37e3c-118">`Startup`类具有以下添加内容：</span><span class="sxs-lookup"><span data-stu-id="37e3c-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="37e3c-119">内部`Startup.ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="37e3c-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="37e3c-120">默认值 UI 标识：</span><span class="sxs-lookup"><span data-stu-id="37e3c-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="37e3c-121">IdentityServer 具有附加`AddApiAuthorization`帮助器方法，该设置一些默认 IdentityServer 之上的 ASP.NET Core 约定：</span><span class="sxs-lookup"><span data-stu-id="37e3c-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="37e3c-122">使用其他身份验证`AddIdentityServerJwt`IdentityServer 生成配置的应用可验证的 JWT 令牌的帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="37e3c-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="37e3c-123">内部`Startup.Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="37e3c-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="37e3c-124">负责验证请求凭据和在请求上下文上设置用户身份验证中间件：</span><span class="sxs-lookup"><span data-stu-id="37e3c-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="37e3c-125">公开的 Open ID Connect 终结点 IdentityServer 中间件：</span><span class="sxs-lookup"><span data-stu-id="37e3c-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="37e3c-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="37e3c-126">AddApiAuthorization</span></span>

<span data-ttu-id="37e3c-127">此帮助器方法配置 IdentityServer 便使用我们支持的配置。</span><span class="sxs-lookup"><span data-stu-id="37e3c-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="37e3c-128">IdentityServer 是一个功能强大且可扩展的框架，用于处理应用程序安全问题。</span><span class="sxs-lookup"><span data-stu-id="37e3c-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="37e3c-129">同时，该代理公开针对最常见的方案不必要的复杂性。</span><span class="sxs-lookup"><span data-stu-id="37e3c-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="37e3c-130">因此，一组约定和配置选项被提供给您被认为是很好的起点。</span><span class="sxs-lookup"><span data-stu-id="37e3c-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="37e3c-131">一旦你的身份验证需求的变化，IdentityServer 的完整功能是仍可用于自定义身份验证，以满足您的需要。</span><span class="sxs-lookup"><span data-stu-id="37e3c-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="37e3c-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="37e3c-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="37e3c-133">此帮助器方法将应用程序的策略方案配置为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="37e3c-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="37e3c-134">策略配置为让标识处理所有请求路由到的标识 URL 空间中任何子路径"/ 标识"。</span><span class="sxs-lookup"><span data-stu-id="37e3c-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="37e3c-135">`JwtBearerHandler`处理所有其他请求。</span><span class="sxs-lookup"><span data-stu-id="37e3c-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="37e3c-136">此外，此方法注册`<<ApplicationName>>API`IdentityServer 具有默认作用域的 API 资源`<<ApplicationName>>API`并配置 JWT 持有者令牌中间件 IdentityServer 由应用程序颁发的令牌进行验证。</span><span class="sxs-lookup"><span data-stu-id="37e3c-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="37e3c-137">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="37e3c-137">SampleDataController</span></span>

<span data-ttu-id="37e3c-138">在中*Controllers\SampleDataController.cs*文件中，请注意`[Authorize]`属性应用于类，该值指示用户需要经过授权基于访问的资源的默认策略。</span><span class="sxs-lookup"><span data-stu-id="37e3c-138">In the *Controllers\SampleDataController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="37e3c-139">默认授权策略配置为使用默认的身份验证方案，通过设置恰好`AddIdentityServerJwt`上面，使已提到的策略方案`JwtBearerHandler`配置通过此类帮助器方法的默认处理程序向应用程序的请求。</span><span class="sxs-lookup"><span data-stu-id="37e3c-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="37e3c-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="37e3c-140">ApplicationDbContext</span></span>

<span data-ttu-id="37e3c-141">在*Data\ApplicationDbContext.cs*文件中，请注意，相同`DbContext`中标识用于它所扩展的异常`ApiAuthorizationDbContext`(更派生的类从`IdentityDbContext`) 包含用于 IdentityServer 的架构。</span><span class="sxs-lookup"><span data-stu-id="37e3c-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="37e3c-142">若要获取的数据库架构的完全控制权，从一个可用的标识继承`DbContext`类，并配置要通过调用包含标识架构的上下文`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`上`OnModelCreating`方法。</span><span class="sxs-lookup"><span data-stu-id="37e3c-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="37e3c-143">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="37e3c-143">OidcConfigurationController</span></span>

<span data-ttu-id="37e3c-144">在中*Controllers\OidcConfigurationController.cs*文件中，请注意，预配为客户端需要使用的 OIDC 参数提供服务的终结点。</span><span class="sxs-lookup"><span data-stu-id="37e3c-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="37e3c-145">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="37e3c-145">appsettings.json</span></span>

<span data-ttu-id="37e3c-146">在中*appsettings.json*文件的项目根目录中，新增了一个`IdentityServer`描述的列表的部分配置客户端。</span><span class="sxs-lookup"><span data-stu-id="37e3c-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="37e3c-147">在以下示例中，没有单个客户端。</span><span class="sxs-lookup"><span data-stu-id="37e3c-147">In the following example, there's a single client.</span></span> <span data-ttu-id="37e3c-148">客户端名称对应于应用程序名称和 oauth 的约定映射`ClientId`参数。</span><span class="sxs-lookup"><span data-stu-id="37e3c-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="37e3c-149">该配置文件指示要配置的应用程序类型。</span><span class="sxs-lookup"><span data-stu-id="37e3c-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="37e3c-150">它是简化配置过程的服务器的驱动器约定在内部使用。</span><span class="sxs-lookup"><span data-stu-id="37e3c-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="37e3c-151">有多个配置文件，如中所述[应用程序配置文件](#application-profiles)部分。</span><span class="sxs-lookup"><span data-stu-id="37e3c-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="37e3c-152">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="37e3c-152">appsettings.Development.json</span></span>

<span data-ttu-id="37e3c-153">在*appsettings。Development.json*文件的项目根目录中，没有`IdentityServer`部分描述用于令牌签名的密钥。</span><span class="sxs-lookup"><span data-stu-id="37e3c-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="37e3c-154">在部署到生产环境时，密钥需设置和应用程序，以及部署中所述[部署到生产](#deploy-to-production)部分。</span><span class="sxs-lookup"><span data-stu-id="37e3c-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="37e3c-155">在 Angular 应用的一般说明</span><span class="sxs-lookup"><span data-stu-id="37e3c-155">General description of the Angular app</span></span>

<span data-ttu-id="37e3c-156">身份验证和授权 API 支持在 Angular 模板在其自己 Angular 模块中驻留*ClientApp\src\api 授权*目录。</span><span class="sxs-lookup"><span data-stu-id="37e3c-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="37e3c-157">该模块组成的以下元素：</span><span class="sxs-lookup"><span data-stu-id="37e3c-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="37e3c-158">3 个组件：</span><span class="sxs-lookup"><span data-stu-id="37e3c-158">3 components:</span></span>
  * <span data-ttu-id="37e3c-159">*login.component.ts*:处理应用程序的登录流。</span><span class="sxs-lookup"><span data-stu-id="37e3c-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="37e3c-160">*logout.component.ts*:处理应用程序的注销流。</span><span class="sxs-lookup"><span data-stu-id="37e3c-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="37e3c-161">*登录名 menu.component.ts*:一个小组件，显示以下集的链接之一：</span><span class="sxs-lookup"><span data-stu-id="37e3c-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="37e3c-162">用户配置文件管理和注销时用户进行身份验证的链接。</span><span class="sxs-lookup"><span data-stu-id="37e3c-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="37e3c-163">注册和登录时用户未经身份验证的链接。</span><span class="sxs-lookup"><span data-stu-id="37e3c-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="37e3c-164">路由防护`AuthorizeGuard`，可以添加到路由并要求用户进行身份验证，才能访问该路由。</span><span class="sxs-lookup"><span data-stu-id="37e3c-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="37e3c-165">HTTP 侦听器`AuthorizeInterceptor`，将访问令牌附加到传出 HTTP 请求时对用户身份验证以 API 为目标。</span><span class="sxs-lookup"><span data-stu-id="37e3c-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="37e3c-166">一种服务`AuthorizeService`，处理身份验证过程的较低级别的详细信息，并公开有关身份验证的用户使用应用程序的其余部分的信息。</span><span class="sxs-lookup"><span data-stu-id="37e3c-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="37e3c-167">Angular 模块，它定义了路由，与应用程序的身份验证部分相关联。</span><span class="sxs-lookup"><span data-stu-id="37e3c-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="37e3c-168">它公开登录菜单组件、 侦听器、 防护和应用程序的其余部分使用的服务。</span><span class="sxs-lookup"><span data-stu-id="37e3c-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="37e3c-169">React 应用的常规说明</span><span class="sxs-lookup"><span data-stu-id="37e3c-169">General description of the React app</span></span>

<span data-ttu-id="37e3c-170">身份验证和 React 模板中的 API 授权支持驻留在*ClientApp\src\components\api 授权*目录。</span><span class="sxs-lookup"><span data-stu-id="37e3c-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="37e3c-171">它由以下元素组成：</span><span class="sxs-lookup"><span data-stu-id="37e3c-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="37e3c-172">4 个组件：</span><span class="sxs-lookup"><span data-stu-id="37e3c-172">4 components:</span></span>
  * <span data-ttu-id="37e3c-173">*Login.js*:处理应用程序的登录流。</span><span class="sxs-lookup"><span data-stu-id="37e3c-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="37e3c-174">*Logout.js*:处理应用程序的注销流。</span><span class="sxs-lookup"><span data-stu-id="37e3c-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="37e3c-175">*LoginMenu.js*:一个小组件，显示以下集的链接之一：</span><span class="sxs-lookup"><span data-stu-id="37e3c-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="37e3c-176">用户配置文件管理和注销时用户进行身份验证的链接。</span><span class="sxs-lookup"><span data-stu-id="37e3c-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="37e3c-177">注册和登录时用户未经身份验证的链接。</span><span class="sxs-lookup"><span data-stu-id="37e3c-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="37e3c-178">*AuthorizeRoute.js*:需要用户进行身份验证之前呈现组件的路由组件所示`Component`参数。</span><span class="sxs-lookup"><span data-stu-id="37e3c-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="37e3c-179">导出`authService`类的实例`AuthorizeService`，处理身份验证过程的较低级别的详细信息，并公开有关身份验证的用户使用应用程序的其余部分的信息。</span><span class="sxs-lookup"><span data-stu-id="37e3c-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="37e3c-180">现在，已了解该解决方案的主要组件，您可以深入地了解应用程序的各个方案。</span><span class="sxs-lookup"><span data-stu-id="37e3c-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="37e3c-181">需要在上一个新的 API 进行授权</span><span class="sxs-lookup"><span data-stu-id="37e3c-181">Require authorization on a new API</span></span>

<span data-ttu-id="37e3c-182">默认情况下，系统配置为轻松地要求新的 Api 的授权。</span><span class="sxs-lookup"><span data-stu-id="37e3c-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="37e3c-183">若要执行此操作，创建新的控制器，并添加`[Authorize]`属性到控制器类或控制器中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="37e3c-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="37e3c-184">保护客户端的路由 (Angular)</span><span class="sxs-lookup"><span data-stu-id="37e3c-184">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="37e3c-185">保护客户端的路由是通过向临界条件运行时配置的路由的列表中添加 authorize 防护。</span><span class="sxs-lookup"><span data-stu-id="37e3c-185">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="37e3c-186">例如，可以看到如何`fetch-data`主应用 Angular 模块内配置路由：</span><span class="sxs-lookup"><span data-stu-id="37e3c-186">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="37e3c-187">务必提到保护路由不保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)，但它仅会阻止用户未经身份验证时，导航到给定的客户端的路由。</span><span class="sxs-lookup"><span data-stu-id="37e3c-187">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="37e3c-188">API 请求 (Angular) 进行身份验证</span><span class="sxs-lookup"><span data-stu-id="37e3c-188">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="37e3c-189">通过由应用定义的 HTTP 客户端侦听器使用自动完成与应用一起托管的 Api 的请求进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="37e3c-189">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="37e3c-190">保护客户端的路由 (React)</span><span class="sxs-lookup"><span data-stu-id="37e3c-190">Protect a client-side route (React)</span></span>

<span data-ttu-id="37e3c-191">使用保护客户端的路由`AuthorizeRoute`组件而不是纯`Route`组件。</span><span class="sxs-lookup"><span data-stu-id="37e3c-191">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="37e3c-192">例如，请注意如何`fetch-data`中配置路由`App`组件：</span><span class="sxs-lookup"><span data-stu-id="37e3c-192">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="37e3c-193">保护一个路由：</span><span class="sxs-lookup"><span data-stu-id="37e3c-193">Protecting a route:</span></span>

* <span data-ttu-id="37e3c-194">不会保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)。</span><span class="sxs-lookup"><span data-stu-id="37e3c-194">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="37e3c-195">仅当未经身份验证时，导航到给定的客户端的路由阻止用户。</span><span class="sxs-lookup"><span data-stu-id="37e3c-195">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="37e3c-196">API 请求 (React) 进行身份验证</span><span class="sxs-lookup"><span data-stu-id="37e3c-196">Authenticate API requests (React)</span></span>

<span data-ttu-id="37e3c-197">通过 React 的请求进行身份验证通过首先导入`authService`实例与`AuthorizeService`。</span><span class="sxs-lookup"><span data-stu-id="37e3c-197">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="37e3c-198">从检索访问令牌`authService`并附加到请求，如下所示。</span><span class="sxs-lookup"><span data-stu-id="37e3c-198">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="37e3c-199">在 React 组件，这项工作通常是`componentDidMount`生命周期方法，或作为某些用户交互的结果。</span><span class="sxs-lookup"><span data-stu-id="37e3c-199">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="37e3c-200">AuthService 导入你的组件</span><span class="sxs-lookup"><span data-stu-id="37e3c-200">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="37e3c-201">检索并将访问令牌附加到响应</span><span class="sxs-lookup"><span data-stu-id="37e3c-201">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="37e3c-202">部署到生产环境</span><span class="sxs-lookup"><span data-stu-id="37e3c-202">Deploy to production</span></span>

<span data-ttu-id="37e3c-203">若要将应用部署到生产环境，需要将其预配的以下资源：</span><span class="sxs-lookup"><span data-stu-id="37e3c-203">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="37e3c-204">使用数据库来存储标识用户帐户和 IdentityServer 授予。</span><span class="sxs-lookup"><span data-stu-id="37e3c-204">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="37e3c-205">生产证书，用于为令牌签名。</span><span class="sxs-lookup"><span data-stu-id="37e3c-205">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="37e3c-206">此证书; 没有特定要求它可以是自签名的证书或证书的 CA 颁发机构通过预配。</span><span class="sxs-lookup"><span data-stu-id="37e3c-206">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="37e3c-207">它可以通过 PowerShell 或 OpenSSL 等标准工具生成。</span><span class="sxs-lookup"><span data-stu-id="37e3c-207">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="37e3c-208">可以安装到目标计算机上的证书存储或作为部署 *.pfx*使用强密码的文件。</span><span class="sxs-lookup"><span data-stu-id="37e3c-208">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="37e3c-209">示例:将部署到 Azure 网站</span><span class="sxs-lookup"><span data-stu-id="37e3c-209">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="37e3c-210">本部分介绍如何将应用部署到 Azure 网站使用的证书存储中存储的证书。</span><span class="sxs-lookup"><span data-stu-id="37e3c-210">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="37e3c-211">若要修改应用程序，若要从证书存储加载证书，需要上至少应为标准级别时在稍后的步骤中配置应用服务计划。</span><span class="sxs-lookup"><span data-stu-id="37e3c-211">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="37e3c-212">在应用中*appsettings.json*文件中，修改`IdentityServer`部分以包括关键详细信息：</span><span class="sxs-lookup"><span data-stu-id="37e3c-212">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="37e3c-213">证书上的 name 属性对应于该证书的可分辨主题。</span><span class="sxs-lookup"><span data-stu-id="37e3c-213">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="37e3c-214">存储位置表示加载的证书的位置 (`CurrentUser`或`LocalMachine`)。</span><span class="sxs-lookup"><span data-stu-id="37e3c-214">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="37e3c-215">存储名称表示存储证书的证书存储区的名称。</span><span class="sxs-lookup"><span data-stu-id="37e3c-215">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="37e3c-216">在这种情况下，它指向个人用户存储。</span><span class="sxs-lookup"><span data-stu-id="37e3c-216">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="37e3c-217">若要部署到 Azure 网站，部署应用程序中的步骤[将应用部署到 Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)创建所需的 Azure 资源和将应用部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="37e3c-217">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="37e3c-218">后按照前面的说明，该应用程序部署到 Azure，但尚不起作用。</span><span class="sxs-lookup"><span data-stu-id="37e3c-218">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="37e3c-219">应用程序使用的证书仍需要进行设置。</span><span class="sxs-lookup"><span data-stu-id="37e3c-219">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="37e3c-220">查找要使用的证书的指纹，然后按照步骤中所述[加载证书](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)。</span><span class="sxs-lookup"><span data-stu-id="37e3c-220">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="37e3c-221">虽然这些步骤提到 SSL，则**私用证书**用于上传要与该应用程序使用的预配的证书门户上的部分。</span><span class="sxs-lookup"><span data-stu-id="37e3c-221">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="37e3c-222">此步骤后，重新启动应用程序，它应能正常运行。</span><span class="sxs-lookup"><span data-stu-id="37e3c-222">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="37e3c-223">其他配置选项</span><span class="sxs-lookup"><span data-stu-id="37e3c-223">Other configuration options</span></span>

<span data-ttu-id="37e3c-224">对 API 授权支持基于 IdentityServer 具有一系列约定、 默认值和增强功能，以简化 Spa 的体验。</span><span class="sxs-lookup"><span data-stu-id="37e3c-224">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="37e3c-225">不用说，IdentityServer 的完整功能后，在后台的 ASP.NET Core 集成不涵盖你的方案。</span><span class="sxs-lookup"><span data-stu-id="37e3c-225">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="37e3c-226">ASP.NET Core 支持侧重于"第一方"应用程序，其中创建和部署我们的组织的所有应用。</span><span class="sxs-lookup"><span data-stu-id="37e3c-226">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="37e3c-227">在这种情况下，不是等同意或联合身份验证提供支持。</span><span class="sxs-lookup"><span data-stu-id="37e3c-227">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="37e3c-228">对于这些方案中，使用 IdentityServer 并遵循其文档。</span><span class="sxs-lookup"><span data-stu-id="37e3c-228">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="37e3c-229">应用程序配置文件</span><span class="sxs-lookup"><span data-stu-id="37e3c-229">Application profiles</span></span>

<span data-ttu-id="37e3c-230">应用程序配置文件是预定义的应用，以进一步定义其参数的配置。</span><span class="sxs-lookup"><span data-stu-id="37e3c-230">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="37e3c-231">在此期间，支持以下配置文件：</span><span class="sxs-lookup"><span data-stu-id="37e3c-231">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="37e3c-232">`IdentityServerSPA`：表示 SPA IdentityServer 作为一个单元一起托管。</span><span class="sxs-lookup"><span data-stu-id="37e3c-232">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="37e3c-233">`redirect_uri`默认为`/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="37e3c-233">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="37e3c-234">`post_logout_redirect_uri`默认为`/authentication/logout-callback`。</span><span class="sxs-lookup"><span data-stu-id="37e3c-234">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="37e3c-235">作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。</span><span class="sxs-lookup"><span data-stu-id="37e3c-235">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="37e3c-236">是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。</span><span class="sxs-lookup"><span data-stu-id="37e3c-236">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="37e3c-237">允许的响应模式是`fragment`。</span><span class="sxs-lookup"><span data-stu-id="37e3c-237">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="37e3c-238">`SPA`：表示不托管与 IdentityServer SPA。</span><span class="sxs-lookup"><span data-stu-id="37e3c-238">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="37e3c-239">作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。</span><span class="sxs-lookup"><span data-stu-id="37e3c-239">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="37e3c-240">是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。</span><span class="sxs-lookup"><span data-stu-id="37e3c-240">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="37e3c-241">允许的响应模式是`fragment`。</span><span class="sxs-lookup"><span data-stu-id="37e3c-241">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="37e3c-242">`IdentityServerJwt`：表示与 IdentityServer 一起托管的 API。</span><span class="sxs-lookup"><span data-stu-id="37e3c-242">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="37e3c-243">应用程序配置为具有单个作用域，默认值为应用程序名称。</span><span class="sxs-lookup"><span data-stu-id="37e3c-243">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="37e3c-244">`API`：表示与 IdentityServer 不托管的 API。</span><span class="sxs-lookup"><span data-stu-id="37e3c-244">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="37e3c-245">应用程序配置为具有单个作用域，默认值为应用程序名称。</span><span class="sxs-lookup"><span data-stu-id="37e3c-245">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="37e3c-246">通过 AppSettings 配置</span><span class="sxs-lookup"><span data-stu-id="37e3c-246">Configuration through AppSettings</span></span>

<span data-ttu-id="37e3c-247">通过将它们添加到的列表可以配置应用，通过配置系统`Clients`或`Resources`。</span><span class="sxs-lookup"><span data-stu-id="37e3c-247">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="37e3c-248">配置每个客户端`redirect_uri`和`post_logout_redirect_uri`属性，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="37e3c-248">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="37e3c-249">在配置资源，可以配置资源的作用域，如下所示：</span><span class="sxs-lookup"><span data-stu-id="37e3c-249">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="37e3c-250">通过代码配置</span><span class="sxs-lookup"><span data-stu-id="37e3c-250">Configuration through code</span></span>

<span data-ttu-id="37e3c-251">您还可以配置的客户端和资源使用的重载通过代码`AddApiAuthorization`的进行的操作以配置选项。</span><span class="sxs-lookup"><span data-stu-id="37e3c-251">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="37e3c-252">其他资源</span><span class="sxs-lookup"><span data-stu-id="37e3c-252">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
