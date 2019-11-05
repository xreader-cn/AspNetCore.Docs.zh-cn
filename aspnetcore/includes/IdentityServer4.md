ASP.NET Core 标识将用户界面 (UI) 登录功能添加到 ASP.NET Core Web 应用。 若要保护 Web API 和 SPA，请使用以下项之一：

* [Azure Active Directory](/azure/api-management/api-management-howto-protect-backend-with-aad)
* [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-custom-rest-api-netfw) (Azure AD B2C)]
* [IdentityServer4](https://identityserver.io)

IdentityServer4 是适用于 ASP.NET Core 3.0 的 OpenID Connect 和 OAuth 2.0 框架。 IdentityServer4 支持以下安全功能：

* 身份验证即服务 (AaaS)
* 跨多个应用程序类型的单一登录/注销 (SSO)
* API 的访问控制
* Federation Gateway

有关详细信息，请参阅[欢迎使用 IdentityServer4](http://docs.identityserver.io/en/latest/index.html)。