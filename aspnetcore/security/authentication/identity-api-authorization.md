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
# <a name="authentication-and-authorization-for-spas"></a>身份验证和授权 Spa

在 ASP.NET 3.0 中，我们引入对 API 授权使用我们的新支持的单页面应用程序中的身份验证的支持。 这种支持基于 ASP.NET Core 标识的组合进行身份验证以及用于实现 Open ID Connect 存储用户和标识服务器。

我们已添加一个新的身份验证参数对我们 Angular 和 React 模板类似于与我们 mvc 和 razor 页面的模板中的身份验证参数的允许值 None 和个人。

## <a name="create-an-angular-app-with-api-authorization-support"></a>使用 API 身份验证支持创建 Angular 应用

若要使用支持身份验证和授权的用户创建新的 Angular 应用程序，打开命令 shell 并运行以下命令：

```console
dotnet new angular -o <output_directory_name> -au Individual
```

上述命令创建与 ASP.NET Core 应用*ClientApp*目录，其中包含 Angular 应用。

## <a name="create-a-react-app-with-api-authorization-support"></a>使用 API 身份验证支持创建 React 应用

若要创建新的 React 应用具有支持的身份验证和授权的用户，打开命令 shell 并运行以下命令：

```console
dotnet new react -o <output_directory_name> -au Individual
```

上述命令创建与 ASP.NET Core 应用*ClientApp*目录，其中包含 React 应用。

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>应用程序的 ASP.NET Core 组件的一般说明

我们包括支持的身份验证时，有几个添加到项目：

### <a name="startup-class"></a>启动类

如果我们看一下下面的启动类中的代码，我们可以非常感谢以下包含项：
* 内部`public void ConfigureServices(IServiceCollection services)`:
  * 默认值 UI 标识。
    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
      options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
      .AddDefaultUI(UIFramework.Bootstrap4)
      .AddEntityFrameworkStores<ApplicationDbContext>();
    ```
  * 标识服务器采用其他 AddApiAuthorization 帮助器方法，设置一些默认 ASP.NET 约定基于标识服务器。
    ```csharp
    services.AddIdentityServer()
      .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```
  * 使用其他 AddIdentityServerJwt 帮助器方法，用于配置应用程序来验证 Jwt 令牌生成的标识服务器进行身份验证。 
    ```csharp
    services.AddAuthentication()
      .AddIdentityServerJwt();
    ```
* 内部`public void Configure(IApplicationBuilder app)`:
  * 负责验证传入请求中的凭据和在请求上下文上设置用户身份验证中间件。
    ```csharp
    app.UseAuthentication();
    ```
  * 标识服务器中间件，它公开 Open ID Connect 终结点。
    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization 
此帮助器方法标识将服务器配置为使用我们支持的配置。 标识服务器是一个功能强大且可扩展的框架，用于处理应用程序安全问题，但在公开许多我们无需了解最常见的方案的复杂性的同时，因此我们选择的一组约定和我们认为您的配置选项都很好的起点。 一旦你的身份验证需求的变化标识服务器的完整功能是仍可用于你，因此您可以进行自定义以满足你的需求。

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt
此帮助器方法将应用程序的策略方案配置为默认身份验证处理程序。 策略配置为让标识处理所有请求，请转到标识 url 空间中任何子路径"/ 标识"并使能够都处理所有其他请求 JwtBearerHandler。
此方法注册的 Addionally`<<ApplicationName>>API`具有默认作用域的标识服务器使用的 Api 资源`<<ApplicationName>>API`并配置 JWT 持有者令牌中间件来验证应用程序颁发的标识服务器令牌。

### <a name="sampledatacontroller"></a>SampleDataController
如果我们看一下文件 Controllers\SampleDataController.cs 我们可以观察`[Authorize]`属性应用于类，该值指示用户需要经过授权基于访问的资源的默认策略。 默认授权策略配置为使用的默认身份验证方案，通过设置恰好`AddIdentityServerJwt`我们前面所述的策略方案，以 JwtBearer 处理程序配置通过此类帮助器方法的默认处理程序向应用程序的请求。

### <a name="applicationdbcontext"></a>ApplicationDbContext
如果我们看一下在 Data\ApplicationDbContext.cs 文件我们可以看到我们使用标识与异常中，它将扩展 ApiAuthorizationDbContext （从 IdentityDbContext 的派生程度更大类） 的同一 DbContext 包含用于标识服务器的架构。
如果我们要完全控制的数据库架构我们只需从一个可用的标识 DbContext 类继承并配置要通过调用包含标识架构的上下文`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`上`OnModelCreating`方法。

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController
如果我们看一下文件 Controllers\OidcConfigurationController.cs 我们可以看到该终结点，我们站立来提供客户端需要使用的 OIDC 参数。

### <a name="appsettingsjson"></a>appsettings.json
如果我们查看项目的根目录上的 appsettings.json 文件，我们可以看到一个新`IdentityServer`部分中描述的列表配置客户端，以及我们可以看到没有单个客户端。 客户端的名称对应于应用程序的名称，并通过约定 oAuth ClientId 参数映射。 配置文件指示我们要配置的应用程序的类型并使用它在内部驱动器约定，可简化对服务器的配置过程。 有多个配置文件可在下面的部分中所述。

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.json
如果我们看一下 appsettings。Development.json 文件在项目的根目录，我们可以看到一个新`IdentityServer`部分介绍了我们使用的令牌进行签名的密钥。 部署到生产环境时键需要预配并连同应用程序一起部署，如下所述。

```json
  "IdentityServer": {
    "Key": {
      "Type": "Development"
    }
  }
}
```

## <a name="general-description-of-the-angular-application"></a>Angular 应用程序的一般说明
支持身份验证和 Angular 模板中的 API 授权位于其自己的 Angular 模块。 ClientApp\src\api 授权和其下是由以下元素组成：
* 3 个组件：
  * 登录名组件：处理应用程序的登录流。
  * 注销组件：处理应用程序的注销流程。
  * 登录名菜单组件：显示当前的小组件包含链接的用户来管理用户配置文件和注销进行身份验证或链接来登录或注册时用户未经过身份验证。
* 路由防护`AuthorizeGuard`，可以添加到路由并要求用户进行身份验证，才能访问该路由。
* Http 侦听器`AuthorizeInterceptor`，将访问令牌附加到传出 HTTP 请求时对用户身份验证以 API 为目标。
* 一种服务`AuthorizeService`，处理身份验证过程的较低级别详细信息，并公开应用程序供使用的其他部分已经过身份验证的用户的信息。
* 定义了路由的 angular 模块与应用程序的身份验证部分相关联，并公开登录菜单组件、 侦听器、 防护和应用程序的其余部分使用的服务。

## <a name="general-description-of-the-react-application"></a>React 应用程序的一般说明
支持身份验证和 ClientApp\src\components\api authorization\ 和其下的 React 模板生活中的 API 授权由以下元素组成：
* 4 个组件：
  * 登录名组件：处理应用程序的登录流。
  * 注销组件：处理应用程序的注销流程。
  * 登录名菜单组件：显示当前的小组件包含链接的用户来管理用户配置文件和注销进行身份验证或链接来登录或注册时用户未经过身份验证。
  * AuthorizeRoute:需要呈现组件参数中指示的组件之前进行身份验证的用户的路由组件。
  * 导出`authService`类的实例`AuthorizeService`，处理身份验证过程的较低级别详细信息，并公开应用程序供使用的其他部分已经过身份验证的用户的信息。

现在，我们已看到解决方案的主要组件，我们可能需要在应用程序的各个方案具有特定的外观：

## <a name="requiring-authorization-on-a-new-api"></a>要求在一个新的 API 的授权
系统配置现成以使其更容易为新的 Api 需要授权。 为此，只需创建新的控制器并添加`[Authorize]`属性到控制器类或控制器中的任何操作。

## <a name="protecting-a-client-side-route-angular"></a>保护客户端的路由 (Angular)
保护客户端端路由是通过向临界条件运行时配置的路由的列表中添加 authorize 防护。 作为示例可以看到主应用 angular 模块中提取数据路由的配置方式：

```ts
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

务必提到保护路由不保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)，但它仅会阻止用户导航到给定客户端端路由时未经过身份验证。

## <a name="authenticate-api-requests-angular"></a>API 请求 (Angular) 进行身份验证

通过应用程序定义的 HTTP 客户端侦听器使用自动完成应用程序一起托管的 api 的请求进行身份验证。

## <a name="protect-a-client-side-route-react"></a>保护客户端的路由 (React)

保护客户端端路由可通过使用 AuthorizeRoute 组件而不普通的路由组件。 作为示例可以看到应用组件中提取数据路由的配置方式：

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

务必提到保护路由不保护实际的终结点 (这仍然需要`[Authorize]`特性应用于它)，但它仅会阻止用户导航到给定客户端端路由时未经过身份验证。

## <a name="authenticate-api-requests-react"></a>API 请求 (React) 进行身份验证

通过 react 的请求进行身份验证通过首先导入`authService`实例与`AuthorizeService`从 authService 检索访问令牌并将其附加到请求，如下所示。 React 组件中这通常是 componentDidMount 生命周期方法中或作为结果从某些用户交互。

### <a name="import-the-authservice-into-your-component"></a>AuthService 导入你的组件

```js
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>检索并将访问令牌附加到响应

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

## <a name="deploy-into-production"></a>部署到生产环境

若要部署到生产环境的应用程序需要预配多个资源：
* 使用数据库来存储标识用户帐户和标识服务器授予。
* 生产证书，用于为令牌签名。
  * 此证书; 没有特定要求它可以是自签名的证书或证书的 CA 颁发机构通过预配。
  * 它可以通过 powershell 或 openssl 等标准工具生成。
  * 可以安装到目标计算机上的证书存储或部署为使用强密码的 pfx 文件。

### <a name="example-deploy-into-azure-websites"></a>示例:将部署到 Azure 网站

在本部分中我们要将应用部署到 Azure 网站使用的证书存储中存储的证书。 我们需要修改应用程序以从证书存储加载证书。 若要执行此操作，我们的应用服务计划需要我们在后面的步骤配置时，至少为标准层上。 在我们的应用程序中我们只需修改 appsettings.json，以包括关键详细信息上的 IdentityServer 部分：
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
* 证书上的 name 属性对应于该证书的可分辨主题。
* 存储位置表示从 （CurrentUrser 或 LocalMachine） 加载该证书的位置。
* 存储名称表示证书存储，在这种情况下，它指向个人用户存储中的证书存储的名称。

若要部署到 Azure 网站，部署应用程序中的步骤[将应用部署到 Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)创建所需的 Azure 资源和将应用部署到生产环境。

执行此操作之后, 该应用程序部署到 Azure 但还不是完全可用我们仍需设置要由应用程序使用的证书。 若要执行此操作，我们必须具备我们将使用并遵循的步骤中所述的证书的指纹[加载证书](/azure/app-service/app-service-web-ssl-cert-load#load-your-certificates)。

这些步骤指出 SSL，我们可以上传要使用我们的应用程序与我们预配的证书在门户还有"私有证书"部分。

此步骤后，我们应该能够重新启动应用程序，它应能完全正常运行。

## <a name="other-configuration-options"></a>其他配置选项
我们现在支持 API 授权使用的约定、 默认值和增强功能，以简化为单一页面应用程序体验的一组基于标识服务器。 不用说，标识服务器的完整功能后，在幕后我们提供的集成不涵盖你的方案。 我们的支持被侧重于我们所说的"第一方"应用程序，其中创建和部署我们的组织的所有应用程序。 这种情况下，我们不提供对诸如同意或联合身份验证的支持。 对于这些情况下我们的建议是使用标识服务器并遵循其文档。

### <a name="application-profiles"></a>应用程序配置文件
应用程序配置文件是预定义的应用程序，以进一步定义其参数的配置。 目前我们支持两个配置文件：
* IdentityServerSPA:表示与标识服务器作为一个单元一起托管的单页面应用程序。
  * 默认为 redirect_uri `/authentication/login-callback`。
  * 默认为 post_logout_redirect_uri `/authentication/logout-callback`。
  * 作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。
  * 是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。
  * 允许的响应模式是`fragment`。
* SPA:表示不使用标识服务器承载的单页面应用。
  * 作用域组包括`openid`， `profile`，并为应用程序中的 Api 定义每个作用域。
  * 是允许的 OIDC 响应类型套`id_token token`或每个单独 (`id_token`， `token`)。
  * 允许的响应模式是`fragment`。
* IdentityServerJwt:表示与标识服务器一起托管的 API。
  * 应用程序配置为具有单个作用域，默认值为应用程序名称。
* API:表示与标识服务器未托管的 API。
  * 应用程序配置为具有单个作用域，默认值为应用程序名称。

### <a name="configuration-through-appsettings"></a>通过 AppSettings 配置
通过分别将它们添加到客户端或资源的列表，我们可以通过我们的配置系统中配置应用程序。 

配置客户端时我们可以配置`redirect_uri`和`post_logout_redirect_uri`，如下所示：
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

配置资源时我们可以配置该资源的作用域，如下所示：
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

### <a name="configuration-through-code"></a>通过代码配置
我们还可以配置的客户端和资源使用的重载进行的操作以配置选项的 AddApiAuthorization 通过代码。
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
