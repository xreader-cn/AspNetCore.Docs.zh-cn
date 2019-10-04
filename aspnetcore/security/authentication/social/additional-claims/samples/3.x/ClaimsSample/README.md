# <a name="additional-claims-sample-app"></a>其他声明示例应用

示例应用演示了如何：

* 从 Google 获取用户的名字和姓氏，并使用 Google 提供的值存储名称声明。
* 将 Google 访问令牌存储在用户的 @no__t 中。

使用示例应用：

1. 注册应用并获取有效的客户端 ID 和客户端密码以进行 Google 身份验证。 有关详细信息，请参阅[Google 外部登录设置](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins)。
1. 在 `Startup.ConfigureServices` 的[GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)中提供应用程序的客户端 ID 和客户端机密。
1. 运行应用并请求 "我的声明" 页。 如果用户未登录，应用将重定向到 Google。 用 Google 登录。 Google 会将用户重定向回应用程序（`/MyClaims`）。 用户进行身份验证，并加载 "我的声明" 页。 具有 Google 提供的值的 "**用户声明**" 下存在给定名称和姓氏声明。 访问令牌显示在 "**身份验证属性**" 下。
