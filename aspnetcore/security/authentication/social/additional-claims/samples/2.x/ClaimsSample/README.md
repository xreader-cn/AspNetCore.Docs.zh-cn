# <a name="additional-claims-sample-app"></a>其他声明示例应用

示例应用演示了如何：

* 从 Google 获取用户的名字和姓氏，并将名称声明存储由 Google 提供的值。
* 将 Google 访问令牌存储在用户的`AuthenticationProperties`。

若要使用示例应用程序：

1. 注册应用程序和获取有效的客户端 ID 和 Google 身份验证的客户端机密。 有关详细信息，请参阅[Google 外部登录安装程序](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins)。
1. 提供的客户端 ID 和客户端密钥对中的应用程序[GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)的`Startup.ConfigureServices`。
1. 运行应用并请求我声明页。 如果用户未登录，该应用将重定向到 Google。 使用 Google 登录。 Google 将用户重定向回应用程序 (`/MyClaims`)。 用户进行身份验证，并加载我的声明页。 名字和姓氏声明下没有**用户声明**由 Google 提供的值。 下显示的访问令牌**身份验证属性**。
