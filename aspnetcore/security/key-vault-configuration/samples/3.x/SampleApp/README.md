# <a name="key-vault-configuration-provider-sample-app"></a>Key Vault 配置提供程序示例应用程序

此示例演示如何使用 Azure Key Vault 配置提供程序。

该示例使用*Program.cs*文件顶部的 `#define` 语句确定的两种模式之一运行。 有关说明，请参阅[示例代码中的预处理器指令](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code)：

* `Certificate` &ndash; 演示了如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。 可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。
* `Managed` &ndash; 演示了如何使用 Azure 的[托管服务标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)对应用进行身份验证 Azure Key Vault，以通过 Azure AD 身份验证进行身份验证，而无需在应用的代码或配置中使用凭据。 应用使用 Azure Key Vault 进行身份验证时，不需要 Azure AD 的客户端 ID 和机密。 必须将此示例部署到 Azure App Service，才能浏览托管的标识 scearnio。

有关详细信息，请参阅[Azure Key Vault 配置提供程序](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration)。
