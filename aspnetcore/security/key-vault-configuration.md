---
title: 在 ASP.NET Core 中的 azure 密钥保管库配置提供程序
author: guardrex
description: 了解如何使用 Azure 密钥保管库配置提供程序来配置应用程序使用在运行时加载的名称 / 值对。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 78c63cf135ca92f0b5f6c6828b2ae34a44a7b36c
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621019"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>在 ASP.NET Core 中的 azure 密钥保管库配置提供程序

通过[Luke Latham](https://github.com/guardrex)和[Andrew Stanton-nurse](https://github.com/anurse)

本文档介绍如何使用[Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)配置提供程序将从 Azure Key Vault 机密中加载应用配置值。 Azure Key Vault 是基于云的服务，可帮助保护加密密钥和机密使用的应用和服务。 使用 ASP.NET Core 应用使用 Azure 密钥保管库的常见方案包括：

* 控制对敏感的配置数据的访问。
* 存储配置数据时，满足的要求 FIPS 140-2 级别 2 验证硬件安全模块 (HSM)。

这种情况下是可用于应用程序面向 ASP.NET Core 2.1 或更高版本。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="packages"></a>包

若要使用 Azure 密钥保管库配置提供程序，添加到包引用[Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包。

若要采用[托管于 Azure 资源的标识](/azure/active-directory/managed-identities-azure-resources/overview)方案中，添加到包引用[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)包。

> [!NOTE]
> 在撰写本文时，最新稳定版本的`Microsoft.Azure.Services.AppAuthentication`，版本`1.0.3`，提供对支持[系统分配给托管标识](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka)。 为支持*用户分配托管标识*现已推出`1.2.0-preview2`包。 本主题演示如何使用系统管理的标识，并提供的示例应用使用版本`1.0.3`的`Microsoft.Azure.Services.AppAuthentication`包。

## <a name="sample-app"></a>示例应用

由两种模式之一运行示例应用`#define`顶部的语句*Program.cs*文件：

* `Certificate` &ndash; 演示如何对 Azure Key Vault 中存储的访问密钥的 Azure 密钥保管库客户端 ID 和 X.509 证书的使用。 可以从部署到 Azure 应用服务或任何主机能够为 ASP.NET Core 应用提供服务的任何位置运行此版本的示例。
* `Managed` &ndash; 演示如何使用[托管于 Azure 资源的标识](/azure/active-directory/managed-identities-azure-resources/overview)应用进行身份验证对 Azure Key Vault 与 Azure AD 身份验证而无需在应用程序的代码或配置中存储的凭据。 当使用管理的标识进行身份验证，不需要的 Azure AD 应用程序 ID 和密码 （客户端机密）。 `Managed`示例的版本必须部署到 Azure。 遵循中的指导[使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources)部分。

有关如何配置使用预处理器指令的示例应用程序的详细信息 (`#define`)，请参阅<xref:index#preprocessor-directives-in-sample-code>。

## <a name="secret-storage-in-the-development-environment"></a>在开发环境中的机密存储

设置本地使用的机密[机密管理器工具](xref:security/app-secrets)。 机密时在开发环境中在本地计算机上运行示例应用程序，将加载本地机密管理器存储中。

机密管理器工具需要`<UserSecretsId>`应用的项目文件中的属性。 设置属性值 (`{GUID}`) 为任何唯一的 GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

以名称-值对的形式创建机密。 层次结构的值 （配置节） 使用`:`（冒号） 作为分隔符，在[ASP.NET Core 配置](xref:fundamentals/configuration/index)密钥名称。

机密管理器用于从命令行界面打开项目的内容根，其中`{SECRET NAME}`名称和`{SECRET VALUE}`的值：

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

在项目的内容根设置示例应用程序机密从命令行界面中执行以下命令：

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

如果为这些机密存储在 Azure 密钥保管库中[使用 Azure 密钥保管库在生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault)部分中，`_dev`后缀更改为`_prod`。 后缀提供了在应用程序的输出中的源的配置值，该值指示一个视觉提示。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>使用 Azure 密钥保管库在生产环境中的机密存储

提供的说明[快速入门：设置和使用 Azure CLI 的 Azure Key Vault 中检索机密](/azure/key-vault/quick-create-cli)主题摘要如下用于创建 Azure 密钥保管库和存储使用的示例应用程序机密。 请参阅本主题的更多详细信息。

1. 使用中的以下方法之一打开 Azure Cloud shell [Azure 门户](https://portal.azure.com/):

   * 选择**试用**代码块右上角。 在文本框中使用的搜索字符串"Azure CLI"。
   * 与在浏览器中打开 Cloud Shell**启动 Cloud Shell**按钮。
   * 选择**Cloud Shell**在 Azure 门户的右上角菜单上的按钮。

   有关详细信息，请参阅[Azure 命令行接口 (CLI)](/cli/azure/)并[Azure Cloud Shell 的概述](/azure/cloud-shell/overview)。

1. 如果你不已通过身份验证，登录时使用`az login`命令。

1. 使用以下命令创建资源组位置`{RESOURCE GROUP NAME}`是新的资源组的资源组名称和`{LOCATION}`是 Azure 区域 （数据中心）：

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 使用以下命令在资源组中创建密钥保管库位置`{KEY VAULT NAME}`是新的密钥保管库的名称和`{LOCATION}`是 Azure 区域 （数据中心）：

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 以名称-值对的形式，在密钥保管库中创建机密。

   Azure 密钥保管库机密名称被限制为字母数字字符和短划线。 层次结构的值 （配置节） 使用`--`（两个短划线） 作为分隔符。 通常用于分隔部分中的一个子项中的冒号[ASP.NET Core 配置](xref:fundamentals/configuration/index)，不允许在密钥保管库机密名称。 因此，两个短划线的使用和机密加载到应用的配置时交换的冒号。

   以下密文密码是用于示例应用。 值包括`_prod`后缀，以便区分从`_dev`后缀用户机密从开发环境中加载的值。 替换为`{KEY VAULT NAME}`之前步骤中创建的密钥保管库的名称：

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>对 Azure 托管的应用程序使用应用程序 ID 和 X.509 证书

配置 Azure AD，Azure 密钥保管库，应用程序要使用 Azure Active Directory 应用程序 ID 和 X.509 证书对密钥保管库进行身份验证**当应用程序承载在 Azure 外部**。 有关详细信息，请参阅[关于密钥、 机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。

> [!NOTE]
> 尽管对于在 Azure 中托管的应用支持使用应用程序 ID 和 X.509 证书，但我们建议使用[托管于 Azure 资源的标识](#use-managed-identities-for-azure-resources)托管在 Azure 中的应用时。 管理的标识不需要将证书存储在应用程序或开发环境。

示例应用使用应用程序 ID 和 X.509 证书何时`#define`顶部的语句*Program.cs*文件设置为`Certificate`。

1. 创建 PKCS #12 存档 (*.pfx*) 证书。 用于创建证书的选项包括[在 Windows 上的 MakeCert](/windows/desktop/seccrypto/makecert)并[OpenSSL](https://www.openssl.org/)。
1. 将证书安装到当前用户的个人证书存储区。 将该键标记为可导出是可选的。 请注意此过程中更高版本使用的证书的指纹。
1. 导出 PKCS #12 存档 (*.pfx*) 与 (DER） 编码的证书的证书 (*.cer*)。
1. 与 Azure AD 中注册应用程序 (**应用注册**)。
1. 上传的 DER 编码的证书 (*.cer*) 到 Azure AD:
   1. 在 Azure AD 中选择的应用。
   1. 导航到**证书和机密**。
   1. 选择**上传证书**来上传包含公钥的证书。 一个 *.cer*， *.pem*，或 *.crt*证书是可接受。
1. 在应用的存储密钥保管库名称、 应用程序 ID 和证书指纹*appsettings.json*文件。
1. 导航到**密钥保管库**在 Azure 门户中。
1. 选择你在中创建的密钥保管库[使用 Azure 密钥保管库在生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault)部分。
1. 选择**访问策略**。
1. 选择**添加新**。
1. 选择**选择主体**并按名称选择已注册的应用。 选择**选择**按钮。
1. 打开**机密权限**，并提供应用程序与**获取**并**列表**权限。
1. 选择 **确定**。
1. 选择“保存”。
1. 将应用部署。

`Certificate`示例应用程序获取从其配置值`IConfigurationRoot`具有作为机密名称相同的名称：

* 非层次结构的值：值`SecretName`一起被获取`config["SecretName"]`。
* 层次结构的值 （部分）：使用`:`（冒号） 表示法或`GetSection`扩展方法。 使用任一方法获取的配置值：
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 证书是由操作系统管理。 应用程序调用`AddAzureKeyVault`提供的值与*appsettings.json*文件：

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

示例值：

* 密钥保管库名称： `contosovault`
* 应用程序 ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

appsettings.json：

[!code-json[](key-vault-configuration/sample/appsettings.json)]

在运行应用时，网页显示加载的机密值。 在开发环境中，密钥值将加载与`_dev`后缀。 在生产环境中，值将加载与`_prod`后缀。

## <a name="use-managed-identities-for-azure-resources"></a>使用 Azure 资源的托管标识

**应用程序部署到 Azure**可以充分利用[管理 Azure 资源的标识](/azure/active-directory/managed-identities-azure-resources/overview)，它允许应用程序以使用 Azure 密钥保管库进行身份验证使用 Azure AD 身份验证，而无需凭据 (应用程序 ID 和Password/Client 机密) 存储在应用程序中。

示例应用程序使用托管标识的 Azure 资源时`#define`顶部的语句*Program.cs*文件设置为`Managed`。

输入到应用中的保管库名称*appsettings.json*文件。 示例应用程序不需要的应用程序 ID 和密码 （客户端机密） 设置为时`Managed`版本，因此可以忽略这些配置条目。 将应用部署到 Azure 和 Azure 进行身份验证应用程序访问 Azure 密钥保管库仅使用保管库名称存储在*appsettings.json*文件。

将示例应用程序部署到 Azure 应用服务。

与 Azure AD 创建服务时自动注册到 Azure 应用服务部署的应用。 从以下命令中使用的部署中获取的对象 ID。 在 Azure 门户上显示对象 ID**标识**面板中的应用服务。

使用 Azure CLI 和应用程序的对象 ID，提供应用程序与`list`和`get`权限访问密钥保管库：

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

**重启应用**使用 Azure CLI、 PowerShell 或 Azure 门户。

应用程序示例：

* 创建的实例`AzureServiceTokenProvider`类，而连接字符串。 时未提供连接字符串，该提供程序将尝试从 Azure 资源的托管标识获取访问令牌。
* 一个新`KeyVaultClient`使用创建`AzureServiceTokenProvider`实例令牌回调。
* `KeyVaultClient`实例使用的默认实现`IKeyVaultSecretManager`的加载所有机密值，并替换双短划线 (`--`) 用冒号 (`:`) 密钥名称。

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

在运行应用时，网页显示加载的机密值。 在开发环境中，机密值具有`_dev`后缀，因为它们由用户机密提供。 在生产环境中，值将加载与`_prod`后缀，因为它们由 Azure 密钥保管库提供。

如果你收到`Access denied`错误，确认应用注册到 Azure AD 并提供访问密钥保管库。 确认已重新启动了该服务在 Azure 中。

## <a name="use-a-key-name-prefix"></a>使用密钥名称前缀

`AddAzureKeyVault` 提供了接受的实现的重载`IKeyVaultSecretManager`，可用于控制如何密钥保管库机密将转换为配置项。 例如，可以实现的接口来加载基于你在应用启动时提供的前缀值的机密值。 这可以使您例如，若要加载基于应用的版本的密钥。

> [!WARNING]
> 若要将多个应用的机密放入同一个密钥保管库或将环境机密不使用在密钥保管库机密的前缀 (例如，*开发*而不是*生产*机密) 到相同保管库。 我们建议，不同的应用程序和开发/生产环境使用单独的密钥保管库来隔离应用环境的最高级别的安全性。

在以下示例中，机密建立在密钥保管库 （和为开发环境中使用机密管理器工具） 的`5000-AppSecret`（句点不允许在密钥保管库机密名称）。 此密钥表示为 5.0.0.0 版本的应用的应用程序机密。 另一个版本的应用程序中，5.1.0.0，机密添加到密钥保管库 （并使用机密管理器工具） 的`5100-AppSecret`。 每个应用程序版本将其版本控制的机密值加载到作为其配置`AppSecret`、 在加载机密的版本剥离。

`AddAzureKeyVault` 使用自定义调用`IKeyVaultSecretManager`:

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

`IKeyVaultSecretManager`实现应对要加载到配置的正确密钥的机密的版本前缀：

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

`Load`方法调用循环访问保管库机密以找出具有版本前缀的提供程序算法。 当版本前缀找到`Load`，该算法使用`GetKey`方法返回的机密名称的配置名称。 它剥离机密的名称从版本前缀，并返回到应用程序的配置名称-值对的加载的机密名称的其余部分。

当此方法的实现：

1. 应用程序的项目文件中指定的应用程序的版本。 在以下示例中，应用程序的版本设置为`5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. 确认`<UserSecretsId>`应用程序的项目文件中存在属性，则其中`{GUID}`是用户提供的 GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   保存使用在本地的以下机密[机密管理器工具](xref:security/app-secrets):

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 使用以下 Azure CLI 命令在 Azure 密钥保管库中保存的机密：

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 当应用运行时，会加载密钥保管库机密。 字符串机密`5000-AppSecret`与应用程序的项目文件中指定的应用程序的版本匹配 (`5.0.0.0`)。

1. 版本，`5000`去除 （替换为短划线），的键名。 在整个应用，使用密钥读取配置`AppSecret`加载机密值。

1. 如果在项目文件中更改应用的版本`5.1.0.0`并再次运行应用时，返回的机密值是`5.1.0.0_secret_value_dev`开发环境中和`5.1.0.0_secret_value_prod`在生产环境中。

> [!NOTE]
> 您还可以提供您自己`KeyVaultClient`实现`AddAzureKeyVault`。 自定义客户端允许在应用之间共享客户端的单个实例。

## <a name="bind-an-array-to-a-class"></a>将数组绑定至类

提供程序支持的配置值读入数组绑定到 POCO 数组。

从允许包含冒号的键的配置源读取数据时 (`:`) 使用分隔符，数字键段来区分对数组组成的密钥 (`:0:`， `:1:`，... `:{n}:`）格式模式中出现的位置生成。 有关详细信息，请参阅[配置：将数组绑定到一个类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。

Azure 密钥保管库密钥不能使用冒号作为分隔符。 本主题中介绍的方法使用双短划线 (`--`) 作为 （部分） 的层次结构值的分隔符。 使用双短划线和数值的关键段数组密钥存储在 Azure 密钥保管库 (`--0--`， `--1--`， &hellip; `--{n}--`)。

请参阅以下[Serilog](https://serilog.net/)日志记录提供程序配置提供的 JSON 文件。 有两个对象中定义的文本`WriteTo`数组，它反映两个 Serilog*接收器*，其中介绍了日志记录输出的目标：

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

在上面的 JSON 文件中所示的配置存储在 Azure 密钥保管库中使用双短划线 (`--`) 表示法和数字段：

| 键 | 值 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>重新加载机密

机密缓存，直至`IConfigurationRoot.Reload()`调用。 已过期，已禁用，并且已更新密钥保管库中的机密不遵循之前应用此`Reload`执行。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>已禁用和过期的机密

已禁用并已过期机密引发`KeyVaultClientException`。 若要防止应用引发，将为您的应用程序或更新已禁用或已过期机密。

## <a name="troubleshoot"></a>疑难解答

如果应用程序无法加载使用提供程序的配置，一条错误消息写入到[ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。 以下条件下将不加载配置：

* 在应用程序或证书未在 Azure Active Directory 中正确配置。
* 密钥保管库不存在于 Azure 密钥保管库。
* 应用程序无权访问密钥保管库。
* 访问策略不包括`Get`和`List`权限。
* 在密钥保管库，配置数据 （名称 / 值对） 是错误命名为，缺少，禁用，或已过期。
* 应用了错误的密钥保管库名称 (`KeyVaultName`)，Azure AD 应用程序 Id (`AzureADApplicationId`)，或 Azure AD 证书指纹 (`AzureADCertThumbprint`)。
* 配置密钥 （名称） 不正确的应用中的想要加载的值。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/configuration/index>
* [Microsoft Azure:密钥保管库](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure:密钥保管库文档](/azure/key-vault/)
* [如何生成和传输受 HSM 保护密钥的 Azure 密钥保管库](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 类](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [快速入门：设置和使用.NET web 应用从 Azure 密钥保管库检索机密](/azure/key-vault/quick-create-net)
* [教程：如何使用 Azure Windows 虚拟机中使用.NET 中的 Azure 密钥保管库](/azure/key-vault/tutorial-net-windows-virtual-machine)
