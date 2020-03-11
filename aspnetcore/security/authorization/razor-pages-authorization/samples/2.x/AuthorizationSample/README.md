# <a name="aspnet-core-authorization-sample"></a>ASP.NET Core 授权示例

此示例演示如何使用 Razor Pages 按约定的授权。 此示例演示[Razor Pages 授权约定](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization)主题中介绍的功能。

本示例中的用户授权使用 "[使用 cookie 身份验证时不 ASP.NET Core 标识](https://docs.microsoft.com/aspnet/core/security/authentication/cookie)" 主题中所述的 cookie 身份验证功能。 概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。 有关使用 ASP.NET Core 标识的详细信息，请参阅[ASP.NET Core 上的标识简介](https://docs.microsoft.com/aspnet/core/security/authentication/identity)。

使用电子邮件地址 **maria.rodriguez@contoso.com** 可以使用任何密码对用户进行身份验证。 用户在*页面/帐户/登录名. .cs*文件的 `AuthenticateUser` 方法中进行身份验证。 在实际的示例中，用户将对数据库进行身份验证。

## <a name="examples-in-this-sample"></a>此示例中的示例

| Feature | 说明 |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | 将[AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)添加到具有指定路径的页面。 |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | 将[AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)添加到具有指定路径的文件夹中的所有页。 |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | 将[AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)添加到具有指定路径的页面。 |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | 将[AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)添加到具有指定路径的文件夹中的所有页。 |
