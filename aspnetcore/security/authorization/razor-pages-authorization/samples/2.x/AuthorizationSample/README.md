# <a name="aspnet-core-authorization-sample"></a>ASP.NET Core 授权示例

此示例演示使用 Razor 页面授权约定。 此示例演示中所述的功能[Razor 页面授权约定](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization)主题。

在此示例中的用户授权使用 cookie 身份验证功能中所述[使用 cookie 身份验证，而无需 ASP.NET Core标识](https://docs.microsoft.com/aspnet/core/security/authentication/cookie)主题。 概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。 有关使用 ASP.NET Core 标识的信息，请参阅[在 ASP.NET Core 上的标识简介](https://docs.microsoft.com/aspnet/core/security/authentication/identity)。

使用电子邮件地址**maria.rodriguez@contoso.com**任何密码与用户进行身份验证。 用户进行身份验证中`AuthenticateUser`中的方法*Pages/Account/Login.cshtml.cs*文件。 在实际示例中，用户会根据数据库身份验证。

## <a name="examples-in-this-sample"></a>此示例中的示例

| 功能 | 描述 |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | 将添加[AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)到指定的路径包含的页。 |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | 将添加[AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)所有具有指定路径的文件夹中的页。 |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | 将添加[AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)到具有指定路径的页面。 |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | 将添加[AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter)所有具有指定路径的文件夹中的页。 |
