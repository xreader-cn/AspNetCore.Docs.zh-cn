---
title: ASP.NET Core 中的单页面应用程序的身份验证简介
author: ''
description: 使用托管在 ASP.NET Core 应用程序的单页面应用程序标识。
ms.author: ''
ms.date: 03/05/2018
uid: security/authentication/identity/spa
ms.openlocfilehash: cf04ec1ff0ae9afea066fd1864ab0a7956ace32c
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346721"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="35e22-103">身份验证和授权 Spa</span><span class="sxs-lookup"><span data-stu-id="35e22-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="35e22-104">在 ASP.NET 3.0 中，我们引入对 API 授权使用我们的新支持的单页面应用程序中的身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="35e22-104">In ASP.NET 3.0 we are introducing support for authentication in single page applications using our new support for API authorization.</span></span> <span data-ttu-id="35e22-105">这种支持基于 ASP.NET Core 标识的组合进行身份验证以及用于实现 Open ID Connect 存储用户和标识服务器。</span><span class="sxs-lookup"><span data-stu-id="35e22-105">This support is based on a combination of ASP.NET Core Identity for authenticating and storing users and Identity Server for implementing Open ID Connect.</span></span>

<span data-ttu-id="35e22-106">我们已添加一个新的身份验证参数对我们 Angular 和 React 模板类似于与我们 mvc 和 razor 页面的模板中的身份验证参数的允许值 None 和个人。</span><span class="sxs-lookup"><span data-stu-id="35e22-106">We have added a new authentication parameter to our Angular and React templates that is similar to the authentication parameter in our mvc and razor pages templates with allowed values 'None' and 'Individual'.</span></span>

## <a name="create-an-angular-app-with-api-authorization-support"></a><span data-ttu-id="35e22-107">使用 API 身份验证支持创建 Angular 应用</span><span class="sxs-lookup"><span data-stu-id="35e22-107">Create an Angular app with API authorization support</span></span>

<span data-ttu-id="35e22-108">若要使用支持身份验证和授权的用户创建新的 Angular 应用程序，打开命令 shell 并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="35e22-108">To create a new Angular app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="35e22-109">上述命令创建与 ASP.NET Core 应用*ClientApp*目录，其中包含 Angular 应用。</span><span class="sxs-lookup"><span data-stu-id="35e22-109">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the Angular app.</span></span>

## <a name="create-a-react-app-with-api-authorization-support"></a><span data-ttu-id="35e22-110">使用 API 身份验证支持创建 React 应用</span><span class="sxs-lookup"><span data-stu-id="35e22-110">Create a React app with API authorization support</span></span>

<span data-ttu-id="35e22-111">若要创建新的 React 应用具有支持的身份验证和授权的用户，打开命令 shell 并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="35e22-111">To create a new React app with support for authentication and authorization of users, open a command shell and run the following command:</span></span>

```console
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="35e22-112">上述命令创建与 ASP.NET Core 应用*ClientApp*目录，其中包含 React 应用。</span><span class="sxs-lookup"><span data-stu-id="35e22-112">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the React app.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="35e22-113">应用程序的 ASP.NET Core 组件的一般说明</span><span class="sxs-lookup"><span data-stu-id="35e22-113">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="35e22-114">我们包括支持的身份验证时，有几个添加到项目：</span><span class="sxs-lookup"><span data-stu-id="35e22-114">There are several additions to the project when we include support for authentication:</span></span>

### <a name="startup-class"></a><span data-ttu-id="35e22-115">启动类</span><span class="sxs-lookup"><span data-stu-id="35e22-115">Startup class</span></span>

<span data-ttu-id="35e22-116">如果我们看一下下面的启动类中的代码，我们可以非常感谢以下包含项：</span><span class="sxs-lookup"><span data-stu-id="35e22-116">If we look at the code in the Startup class below we can appreciate the following inclusions:</span></span>
* <span data-ttu-id="35e22-117">内部`public void ConfigureServices(IServiceCollection services)`:</span><span class="sxs-lookup"><span data-stu-id="35e22-117">Inside `public void ConfigureServices(IServiceCollection services)`:</span></span>
  * <span data-ttu-id="35e22-118">默认值 UI 标识。</span><span class="sxs-lookup"><span data-stu-id="35e22-118">Identity with the default UI.</span></span>
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * <span data-ttu-id="35e22-119">标识服务器采用其他 AddApiAuthorization 帮助器方法，设置一些默认 ASP.NET 约定基于标识服务器。</span><span class="sxs-lookup"><span data-stu-id="35e22-119">Identity Server with an additional AddApiAuthorization helper method that setups some default ASP.NET Conventions on top of Identity Server.</span></span>
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * <span data-ttu-id="35e22-120">使用其他 AddIdentityServerJwt 帮助器方法，用于配置应用程序来验证 Jwt 令牌生成的标识服务器进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="35e22-120">Authentication with an additional AddIdentityServerJwt helper method that configures the application to validate Jwt tokens produced by Identity Server.</span></span> 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* <span data-ttu-id="35e22-121">内部`public void Configure(IApplicationBuilder app)`:</span><span class="sxs-lookup"><span data-stu-id="35e22-121">Inside `public void Configure(IApplicationBuilder app)`:</span></span>
  * <span data-ttu-id="35e22-122">负责验证传入请求中的凭据和在请求上下文上设置用户身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="35e22-122">The authentication middleware that is responsible for validating the credentials in the incoming request and setting the user on the request context.</span></span>
    ```csharp
    app.UseAuthentication();
    ```
  * <span data-ttu-id="35e22-123">标识服务器中间件，它公开 Open ID Connect 终结点。</span><span class="sxs-lookup"><span data-stu-id="35e22-123">The identity server middleware that exposes the Open ID Connect endpoints.</span></span>
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="35e22-124">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="35e22-124">AddApiAuthorization</span></span> 
<span data-ttu-id="35e22-125">此帮助器方法标识将服务器配置为使用我们支持的配置。</span><span class="sxs-lookup"><span data-stu-id="35e22-125">This helper method configures Identity Server to use our supported configuration.</span></span> <span data-ttu-id="35e22-126">标识服务器是一个功能强大且可扩展的框架，用于处理应用程序安全问题，但在公开许多我们无需了解最常见的方案的复杂性的同时，因此我们选择的一组约定和我们认为您的配置选项都很好的起点。</span><span class="sxs-lookup"><span data-stu-id="35e22-126">Identity Server is a very powerful and extensible framework for handling application security concerns but at the same time that exposes a lot of complexity that we don't need to know about for the most common scenarios, so we choose a set of conventions and configuration options for you that we consider are a good starting point.</span></span> <span data-ttu-id="35e22-127">一旦你的身份验证需求的变化标识服务器的完整功能是仍可用于你，因此您可以进行自定义以满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="35e22-127">Once your authentication needs change the full power of Identity Server is still available to you so you can customize it to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="35e22-128">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="35e22-128">AddIdentityServerJwt</span></span>
<span data-ttu-id="35e22-129">此帮助器方法将应用程序的策略方案配置为默认身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="35e22-129">This helper method configures a policy scheme for the application as the default authentication handler.</span></span> <span data-ttu-id="35e22-130">策略配置为让标识处理所有请求，请转到标识 url 空间中任何子路径"/ 标识"并使能够都处理所有其他请求 JwtBearerHandler。</span><span class="sxs-lookup"><span data-stu-id="35e22-130">The policy is configured to let identity handle all the requests that go to any subpath in the Identity url space "/Identity" and to let the JwtBearerHandler handle all other requests.</span></span>
<span data-ttu-id="35e22-131">此方法注册的 Addionally`<<ApplicationName>>API`具有默认作用域的标识服务器使用的 Api 资源`<<ApplicationName>>API`并配置 JWT 持有者令牌中间件来验证应用程序颁发的标识服务器令牌。</span><span class="sxs-lookup"><span data-stu-id="35e22-131">Addionally this method registers an `<<ApplicationName>>API` Api resource with identity server with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by Identity Server for the application.</span></span>

### <a name="sampledatacontroller"></a><span data-ttu-id="35e22-132">SampleDataController</span><span class="sxs-lookup"><span data-stu-id="35e22-132">SampleDataController</span></span>
<span data-ttu-id="35e22-133">如果我们看一下文件 Controllers\SampleDataController.cs 我们可以观察`[Authorize]`属性应用于类，该值指示用户需要经过授权基于访问的资源的默认策略。</span><span class="sxs-lookup"><span data-stu-id="35e22-133">If we look at the file Controllers\SampleDataController.cs we can observe the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="35e22-134">默认授权策略配置为使用的默认身份验证方案，通过设置恰好`AddIdentityServerJwt`我们前面所述的策略方案，以 JwtBearer 处理程序配置通过此类帮助器方法的默认处理程序向应用程序的请求。</span><span class="sxs-lookup"><span data-stu-id="35e22-134">The default authorization policy happens to be configured to use the default authentication scheme which is set up by `AddIdentityServerJwt` to the policy scheme that we mentioned above, making the JwtBearer handler configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="35e22-135">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="35e22-135">ApplicationDbContext</span></span>
<span data-ttu-id="35e22-136">如果我们看一下在 Data\ApplicationDbContext.cs 文件我们可以看到我们使用标识与异常中，它将扩展 ApiAuthorizationDbContext （从 IdentityDbContext 的派生程度更大类） 的同一 DbContext 包含用于标识服务器的架构。</span><span class="sxs-lookup"><span data-stu-id="35e22-136">If we look at the file in Data\ApplicationDbContext.cs we can see the same DbContext we use in identity with the exception that it extends ApiAuthorizationDbContext (a more derived class from IdentityDbContext) to include the schema for Identity Server.</span></span>
<span data-ttu-id="35e22-137">如果我们要完全控制的数据库架构我们只需从一个可用的标识 DbContext 类继承并配置要通过调用包含标识架构的上下文`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`上`OnModelCreating`方法。</span><span class="sxs-lookup"><span data-stu-id="35e22-137">If we want full control of the database schema we can simply inherit from one of the available Identity DbContext classes and configure the context to include the identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="35e22-138">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="35e22-138">OidcConfigurationController</span></span>
<span data-ttu-id="35e22-139">如果我们看一下文件 Controllers\OidcConfigurationController.cs 我们可以看到该终结点，我们站立来提供客户端需要使用的 OIDC 参数。</span><span class="sxs-lookup"><span data-stu-id="35e22-139">If we look at the file Controllers\OidcConfigurationController.cs we can see the endpoint that we stand-up to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="35e22-140">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="35e22-140">appsettings.json</span></span>
<span data-ttu-id="35e22-141">如果我们查看项目的根目录上的 appsettings.json 文件，我们可以看到一个新`IdentityServer`部分中描述的列表配置客户端，以及我们可以看到没有单个客户端。</span><span class="sxs-lookup"><span data-stu-id="35e22-141">If we look at the appsettings.json file on the root of the project, we can see a new `IdentityServer` section that describes the list of configured clients and we can see that there is a single client.</span></span> <span data-ttu-id="35e22-142">客户端的名称对应于应用程序的名称，并通过约定 oAuth ClientId 参数映射。</span><span class="sxs-lookup"><span data-stu-id="35e22-142">The name of the client corresponds to the name of the application and is mapped by convention to the oAuth ClientId parameter.</span></span> <span data-ttu-id="35e22-143">配置文件指示我们要配置的应用程序的类型并使用它在内部驱动器约定，可简化对服务器的配置过程。</span><span class="sxs-lookup"><span data-stu-id="35e22-143">The profile indicates what type of application we are configuring, and we use it internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="35e22-144">有多个配置文件可在下面的部分中所述。</span><span class="sxs-lookup"><span data-stu-id="35e22-144">There are several profiles available explained in the section below.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="35e22-145">appsettings.Development.json</span><span class="sxs-lookup"><span data-stu-id="35e22-145">appsettings.Development.json</span></span>
<span data-ttu-id="35e22-146">如果我们看一下 appsettings。Development.json 文件在项目的根目录，我们可以看到一个新`IdentityServer`部分介绍了我们使用的令牌进行签名的密钥。</span><span class="sxs-lookup"><span data-stu-id="35e22-146">If we look at the appsettings.Development.json file on the root of the project, we can see a new `IdentityServer` section that describes the key we are using to sign tokens.</span></span> <span data-ttu-id="35e22-147">部署到生产环境时键需要预配并连同应用程序一起部署，如下所述。</span><span class="sxs-lookup"><span data-stu-id="35e22-147">When deploying to production a key needs to be provisioned and deployed alongside the application as explained below.</span></span>

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a><span data-ttu-id="35e22-148">Angular 应用程序的一般说明</span><span class="sxs-lookup"><span data-stu-id="35e22-148">General description of the Angular application</span></span>
<span data-ttu-id="35e22-149">支持身份验证和 Angular 模板中的 API 授权位于其自己的 Angular 模块。</span><span class="sxs-lookup"><span data-stu-id="35e22-149">The support for authentication and API authorization in the Angular template lives in its own Angular module.</span></span> <span data-ttu-id="35e22-150">ClientApp\src\api 授权和其下是由以下元素组成：</span><span class="sxs-lookup"><span data-stu-id="35e22-150">Under ClientApp\src\api-authorization and it is composed of the following elements:</span></span>
* <span data-ttu-id="35e22-151">3 个组件：</span><span class="sxs-lookup"><span data-stu-id="35e22-151">3 Components:</span></span>
  * <span data-ttu-id="35e22-152">登录名组件：处理应用程序的登录流。</span><span class="sxs-lookup"><span data-stu-id="35e22-152">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="35e22-153">注销组件：处理应用程序的注销流程。</span><span class="sxs-lookup"><span data-stu-id="35e22-153">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="35e22-154">登录名菜单组件：显示当前的小组件包含链接的用户来管理用户配置文件和注销进行身份验证或链接来登录或注册时用户未经过身份验证。</span><span class="sxs-lookup"><span data-stu-id="35e22-154">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
* <span data-ttu-id="35e22-155">路由防护`AuthorizeGuard`，可以添加到路由并要求用户进行身份验证，才能访问该路由。</span><span class="sxs-lookup"><span data-stu-id="35e22-155">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="35e22-156">Http 侦听器`AuthorizeInterceptor`，将访问令牌附加到传出 HTTP 请求时对用户身份验证以 API 为目标。</span><span class="sxs-lookup"><span data-stu-id="35e22-156">An http interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="35e22-157">一种服务`AuthorizeService`，处理身份验证过程的较低级别详细信息，并公开应用程序供使用的其他部分已经过身份验证的用户的信息。</span><span class="sxs-lookup"><span data-stu-id="35e22-157">A service `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>
* <span data-ttu-id="35e22-158">定义了路由的 angular 模块与应用程序的身份验证部分相关联，并公开登录菜单组件、 侦听器、 防护和应用程序的其余部分使用的服务。</span><span class="sxs-lookup"><span data-stu-id="35e22-158">An angular module that defines routes associated with the authentication parts of the application and exposes the login menu component, the interceptor, the guard and the service for consumption from the rest of the application.</span></span>

## <a name="general-description-of-the-react-application"></a><span data-ttu-id="35e22-159">React 应用程序的一般说明</span><span class="sxs-lookup"><span data-stu-id="35e22-159">General description of the React application</span></span>
<span data-ttu-id="35e22-160">支持身份验证和 ClientApp\src\components\api authorization\ 和其下的 React 模板生活中的 API 授权由以下元素组成：</span><span class="sxs-lookup"><span data-stu-id="35e22-160">The support for authentication and API authorization in the React template lives under ClientApp\src\components\api-authorization\ and it is composed of the following elements:</span></span>
* <span data-ttu-id="35e22-161">4 个组件：</span><span class="sxs-lookup"><span data-stu-id="35e22-161">4 Components:</span></span>
  * <span data-ttu-id="35e22-162">登录名组件：处理应用程序的登录流。</span><span class="sxs-lookup"><span data-stu-id="35e22-162">Login component: Handles the login flow for the app.</span></span>
  * <span data-ttu-id="35e22-163">注销组件：处理应用程序的注销流程。</span><span class="sxs-lookup"><span data-stu-id="35e22-163">Logout component: Handles the logout flow for the app.</span></span>
  * <span data-ttu-id="35e22-164">登录名菜单组件：显示当前的小组件包含链接的用户来管理用户配置文件和注销进行身份验证或链接来登录或注册时用户未经过身份验证。</span><span class="sxs-lookup"><span data-stu-id="35e22-164">Login menu component: A widget that displays the current authenticated user with links to manage the user profile and log out or links to log in or register when the user is not authenticated.</span></span>
  * <span data-ttu-id="35e22-165">AuthorizeRoute:需要呈现组件参数中指示的组件之前进行身份验证的用户的路由组件。</span><span class="sxs-lookup"><span data-stu-id="35e22-165">AuthorizeRoute: A route component that requires a user to be authenticated before rendering the component indicated in the Component parameter.</span></span>
  * <span data-ttu-id="35e22-166">导出`authService`类的实例`AuthorizeService`，处理身份验证过程的较低级别详细信息，并公开应用程序供使用的其他部分已经过身份验证的用户的信息。</span><span class="sxs-lookup"><span data-stu-id="35e22-166">An exported `authService` instance of class `AuthorizeService` that handles the lower level details of the authentication process and exposes information about the authenticated user to the rest of the application for consumption.</span></span>

<span data-ttu-id="35e22-167">现在，我们已看到解决方案的主要组件，我们可能需要在应用程序的各个方案具有特定的外观：</span><span class="sxs-lookup"><span data-stu-id="35e22-167">Now that we've seen the main components of the solution, we can take a specific look at individual scenarios for the application:</span></span>

## <a name="requiring-authorization-on-a-new-api"></a><span data-ttu-id="35e22-168">要求在一个新的 API 的授权</span><span class="sxs-lookup"><span data-stu-id="35e22-168">Requiring authorization on a new API</span></span>
<span data-ttu-id="35e22-169">系统配置现成以使其更容易为新的 Api 需要授权。</span><span class="sxs-lookup"><span data-stu-id="35e22-169">The system is configured out of the box to make it trivial to require authorization for new APIs.</span></span> <span data-ttu-id="35e22-170">为此，只需创建新的控制器并添加`[Authorize]`属性到控制器类或控制器中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="35e22-170">In order to do so, simply create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="protecting-a-client-side-route-angular"></a><span data-ttu-id="35e22-171">保护客户端的路由 (Angular)</span><span class="sxs-lookup"><span data-stu-id="35e22-171">Protecting a client-side route (Angular)</span></span>
<span data-ttu-id="35e22-172">保护客户端端路由是通过向临界条件运行时配置的路由的列表中添加 authorize 防护。</span><span class="sxs-lookup"><span data-stu-id="35e22-172">Protecting a client side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="35e22-173">作为示例可以看到主应用 angular 模块中提取数据路由的配置方式：</span><span class="sxs-lookup"><span data-stu-id="35e22-173">As an example you can see how the fetch-data route is configured within the main app angular module:</span></span>

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="35e22-174">务必提到保护路由不保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)，但它仅会阻止用户导航到给定客户端端路由时未经过身份验证。</span><span class="sxs-lookup"><span data-stu-id="35e22-174">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="35e22-175">API 请求 (Angular) 进行身份验证</span><span class="sxs-lookup"><span data-stu-id="35e22-175">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="35e22-176">通过应用程序定义的 HTTP 客户端侦听器使用自动完成应用程序一起托管的 api 的请求进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="35e22-176">Authenticating requests to APIs hosted along side the application is done automatically through the use of the HTTP client interceptor defined by the application.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="35e22-177">保护客户端的路由 (React)</span><span class="sxs-lookup"><span data-stu-id="35e22-177">Protect a client-side route (React)</span></span>

<span data-ttu-id="35e22-178">保护客户端端路由可通过使用 AuthorizeRoute 组件而不普通的路由组件。</span><span class="sxs-lookup"><span data-stu-id="35e22-178">Protecting a client side route is done by using the AuthorizeRoute component instead of the plain Route component.</span></span> <span data-ttu-id="35e22-179">作为示例可以看到应用组件中提取数据路由的配置方式：</span><span class="sxs-lookup"><span data-stu-id="35e22-179">As an example you can see how the fetch-data route is configured within the App component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="35e22-180">务必提到保护路由不保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)，但它仅会阻止用户导航到给定客户端端路由时未经过身份验证。</span><span class="sxs-lookup"><span data-stu-id="35e22-180">It is important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client side route when it is not authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="35e22-181">API 请求 (React) 进行身份验证</span><span class="sxs-lookup"><span data-stu-id="35e22-181">Authenticate API requests (React)</span></span>

<span data-ttu-id="35e22-182">通过 react 的请求进行身份验证通过首先导入`authService`实例与`AuthorizeService`从 authService 检索访问令牌并将其附加到请求，如下所示。</span><span class="sxs-lookup"><span data-stu-id="35e22-182">Authenticating requests with react is done by first importing the `authService` instance from the `AuthorizeService` and then retrieving the access token from the authService and attaching it to the request as shown below.</span></span> <span data-ttu-id="35e22-183">React 组件中这通常是 componentDidMount 生命周期方法中或作为结果从某些用户交互。</span><span class="sxs-lookup"><span data-stu-id="35e22-183">In react components this is typically done in the componentDidMount lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="35e22-184">AuthService 导入你的组件</span><span class="sxs-lookup"><span data-stu-id="35e22-184">Import the authService into your component</span></span>

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="35e22-185">检索并将访问令牌附加到响应</span><span class="sxs-lookup"><span data-stu-id="35e22-185">Retrieve and attach the access token to the response</span></span>

```js
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-into-production"></a><span data-ttu-id="35e22-186">部署到生产环境</span><span class="sxs-lookup"><span data-stu-id="35e22-186">Deploy into production</span></span>

<span data-ttu-id="35e22-187">若要部署到生产环境的应用程序需要预配多个资源：</span><span class="sxs-lookup"><span data-stu-id="35e22-187">In order to deploy the application into production we need to provision several resources:</span></span>
* <span data-ttu-id="35e22-188">使用数据库来存储标识用户帐户和标识服务器授予。</span><span class="sxs-lookup"><span data-stu-id="35e22-188">A database to store the Identity user accounts and the identity server grants.</span></span>
* <span data-ttu-id="35e22-189">生产证书，用于为令牌签名。</span><span class="sxs-lookup"><span data-stu-id="35e22-189">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="35e22-190">此证书; 没有特定要求它可以是自签名的证书或证书的 CA 颁发机构通过预配。</span><span class="sxs-lookup"><span data-stu-id="35e22-190">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="35e22-191">它可以通过 powershell 或 openssl 等标准工具生成。</span><span class="sxs-lookup"><span data-stu-id="35e22-191">It can be generated through standard tools like powershell or openssl.</span></span>
  * <span data-ttu-id="35e22-192">可以安装到目标计算机上的证书存储或部署为使用强密码的 pfx 文件。</span><span class="sxs-lookup"><span data-stu-id="35e22-192">It can be installed into the certificate store on the target machines or deployed as a pfx file with a strong password.</span></span>

### <a name="example-deploy-into-azure-websites"></a><span data-ttu-id="35e22-193">示例:将部署到 Azure 网站</span><span class="sxs-lookup"><span data-stu-id="35e22-193">Example: Deploy into Azure Websites</span></span>

<span data-ttu-id="35e22-194">在本部分中我们要将应用部署到 Azure 网站使用的证书存储中存储的证书。</span><span class="sxs-lookup"><span data-stu-id="35e22-194">In this section we are going to deploy the application to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="35e22-195">我们需要修改应用程序以从证书存储加载证书。</span><span class="sxs-lookup"><span data-stu-id="35e22-195">We need to modify the application to load a certicate from the certificate store.</span></span> <span data-ttu-id="35e22-196">若要执行此操作，我们的应用服务计划需要我们在后面的步骤配置时，至少为标准层上。</span><span class="sxs-lookup"><span data-stu-id="35e22-196">To do so, our app service plan needs to be at least on the standard tier when we configure in a later step.</span></span> <span data-ttu-id="35e22-197">在我们的应用程序中我们只需修改 appsettings.json，以包括关键详细信息上的 IdentityServer 部分：</span><span class="sxs-lookup"><span data-stu-id="35e22-197">In our application we simply need to modify the IdentityServer section on appsettings.json to include the key details:</span></span>
```json
  "IdentityServer": {
    "Key": {
      "Type": "Store",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "Name": "CN=MyApplication"
    }
  }
}
```
* <span data-ttu-id="35e22-198">证书上的 name 属性对应于该证书的可分辨主题。</span><span class="sxs-lookup"><span data-stu-id="35e22-198">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="35e22-199">存储位置表示从 （CurrentUrser 或 LocalMachine） 加载该证书的位置。</span><span class="sxs-lookup"><span data-stu-id="35e22-199">The store location represents where to load the certificate from (CurrentUrser or LocalMachine).</span></span>
* <span data-ttu-id="35e22-200">存储名称表示证书存储，在这种情况下，它指向个人用户存储中的证书存储的名称。</span><span class="sxs-lookup"><span data-stu-id="35e22-200">The store name represents the name of the certificate store where the certificate is stored, in this case it points to the personal user store.</span></span>

<span data-ttu-id="35e22-201">若要部署到 Azure 网站，部署应用程序中的步骤[将应用部署到 Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)创建所需的 Azure 资源和将应用部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="35e22-201">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="35e22-202">执行此操作之后, 该应用程序部署到 Azure 但还不是完全可用我们仍需设置要由应用程序使用的证书。</span><span class="sxs-lookup"><span data-stu-id="35e22-202">After doing this, the application is deployed into Azure but is not yet completely functional as we still need to setup the certificate to be used by the application.</span></span> <span data-ttu-id="35e22-203">若要执行此操作，我们必须具备我们将使用并遵循的步骤中所述的证书的指纹[加载证书](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates)。</span><span class="sxs-lookup"><span data-stu-id="35e22-203">To do so, we need to have the thumbprint for the certificate we are going to use and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates).</span></span>

<span data-ttu-id="35e22-204">这些步骤指出 SSL，我们可以上传要使用我们的应用程序与我们预配的证书在门户还有"私有证书"部分。</span><span class="sxs-lookup"><span data-stu-id="35e22-204">While these steps mention SSL, there is a "Private certificates" section on the portal where we can upload our provisioned certificate to use with our app.</span></span>

<span data-ttu-id="35e22-205">此步骤后，我们应该能够重新启动应用程序，它应能完全正常运行。</span><span class="sxs-lookup"><span data-stu-id="35e22-205">After this step, we should be able to restart our application and it should be completely functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="35e22-206">其他配置选项</span><span class="sxs-lookup"><span data-stu-id="35e22-206">Other configuration options</span></span>
<span data-ttu-id="35e22-207">我们现在支持 API 授权使用的约定、 默认值和增强功能，以简化为单一页面应用程序体验的一组基于标识服务器。</span><span class="sxs-lookup"><span data-stu-id="35e22-207">Our support for API authorization builds on top of Identity Server with a set of conventions, default values and enhancements to simplify the experience for Single Page Applications.</span></span> <span data-ttu-id="35e22-208">不用说，标识服务器的完整功能后，在幕后我们提供的集成不涵盖你的方案。</span><span class="sxs-lookup"><span data-stu-id="35e22-208">Needless to say, the full power of Identity Server is available behind the scenes if the integrations that we offer don't cover your scenario.</span></span> <span data-ttu-id="35e22-209">我们的支持被侧重于我们所说的"第一方"应用程序，其中创建和部署我们的组织的所有应用程序。</span><span class="sxs-lookup"><span data-stu-id="35e22-209">Our support is focused on what we call "first-party" applications, where all the applications are created and deployed by our organization.</span></span> <span data-ttu-id="35e22-210">这种情况下，我们不提供对诸如同意或联合身份验证的支持。</span><span class="sxs-lookup"><span data-stu-id="35e22-210">As such we don't offer support for things like consent or federation.</span></span> <span data-ttu-id="35e22-211">对于这些情况下我们的建议是使用标识服务器并遵循其文档。</span><span class="sxs-lookup"><span data-stu-id="35e22-211">For those scenarios our recommendation is to use Identity Server and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="35e22-212">应用程序配置文件</span><span class="sxs-lookup"><span data-stu-id="35e22-212">Application profiles</span></span>
<span data-ttu-id="35e22-213">应用程序配置文件是预定义的应用程序，以进一步定义其参数的配置。</span><span class="sxs-lookup"><span data-stu-id="35e22-213">Application profiles are predefined configurations for applications that further define their parameters.</span></span> <span data-ttu-id="35e22-214">目前我们支持两个配置文件：</span><span class="sxs-lookup"><span data-stu-id="35e22-214">At this time we support two profiles:</span></span>
* <span data-ttu-id="35e22-215">IdentityServerSPA:表示与标识服务器作为一个单元一起托管的单页面应用程序。</span><span class="sxs-lookup"><span data-stu-id="35e22-215">IdentityServerSPA: Represents a single page application hosted alongside Identity Server as a single unit.</span></span>
  * <span data-ttu-id="35e22-216">默认为 redirect_uri `/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="35e22-216">The redirect_uri defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="35e22-217">默认为 post_logout_redirect_uri `/authentication/logout-callback`。</span><span class="sxs-lookup"><span data-stu-id="35e22-217">The post_logout_redirect_uri defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="35e22-218">作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。</span><span class="sxs-lookup"><span data-stu-id="35e22-218">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="35e22-219">是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。</span><span class="sxs-lookup"><span data-stu-id="35e22-219">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="35e22-220">允许的响应模式是`fragment`。</span><span class="sxs-lookup"><span data-stu-id="35e22-220">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="35e22-221">SPA:表示不使用标识服务器承载的单页面应用。</span><span class="sxs-lookup"><span data-stu-id="35e22-221">SPA: Represents a single page application that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="35e22-222">作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。</span><span class="sxs-lookup"><span data-stu-id="35e22-222">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the application.</span></span>
  * <span data-ttu-id="35e22-223">是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。</span><span class="sxs-lookup"><span data-stu-id="35e22-223">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="35e22-224">允许的响应模式是`fragment`。</span><span class="sxs-lookup"><span data-stu-id="35e22-224">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="35e22-225">IdentityServerJwt:表示与标识服务器一起托管的 API。</span><span class="sxs-lookup"><span data-stu-id="35e22-225">IdentityServerJwt: Represents an API that is hosted alongside with Identity Server.</span></span>
  * <span data-ttu-id="35e22-226">应用程序配置为具有单个作用域，默认值为应用程序名称。</span><span class="sxs-lookup"><span data-stu-id="35e22-226">The application is configured to have a single scope that defaults to the application name.</span></span>
* <span data-ttu-id="35e22-227">API:表示与标识服务器未托管的 API。</span><span class="sxs-lookup"><span data-stu-id="35e22-227">API: Represents an API that is not hosted with Identity Server.</span></span>
  * <span data-ttu-id="35e22-228">应用程序配置为具有单个作用域，默认值为应用程序名称。</span><span class="sxs-lookup"><span data-stu-id="35e22-228">The application is configured to have a single scope that defaults to the application name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="35e22-229">通过 AppSettings 配置</span><span class="sxs-lookup"><span data-stu-id="35e22-229">Configuration through AppSettings</span></span>
<span data-ttu-id="35e22-230">通过分别将它们添加到客户端或资源的列表，我们可以通过我们的配置系统中配置应用程序。</span><span class="sxs-lookup"><span data-stu-id="35e22-230">We can configure the applications through our configuration system by adding them to the list of Clients or Resources respectively.</span></span> 

<span data-ttu-id="35e22-231">配置客户端时我们可以配置`redirect_uri`和`post_logout_redirect_uri`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="35e22-231">When configuring clients we can configure the `redirect_uri` and the `post_logout_redirect_uri` as shown below:</span></span>
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

<span data-ttu-id="35e22-232">配置资源时我们可以配置该资源的作用域，如下所示：</span><span class="sxs-lookup"><span data-stu-id="35e22-232">When configuring resources we can configure the scopes for the resource as shown below:</span></span>
```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c",
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="35e22-233">通过代码配置</span><span class="sxs-lookup"><span data-stu-id="35e22-233">Configuration through code</span></span>
<span data-ttu-id="35e22-234">我们还可以配置的客户端和资源使用的重载进行的操作以配置选项的 AddApiAuthorization 通过代码。</span><span class="sxs-lookup"><span data-stu-id="35e22-234">We can also configure the clients and resources through code using an overload of AddApiAuthorization that takes an action to configure options.</span></span>
```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA",
        spa => spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
            .WithLogoutRedirectUri("http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource => resource.WithScopes("a", "b", "c"));
});
```
