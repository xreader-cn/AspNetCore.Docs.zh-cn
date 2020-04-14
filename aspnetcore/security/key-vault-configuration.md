---
title: ASP.NET核心中的 Azure 密钥保管库配置提供程序
author: rick-anderson
description: 了解如何使用 Azure 密钥保管库配置提供程序使用运行时加载的名称值对配置应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228161"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET核心中的 Azure 密钥保管库配置提供程序

由[安德鲁斯坦顿护士](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

本文档介绍如何使用[Microsoft Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)配置提供程序从 Azure 密钥保管库机密加载应用配置值。 Azure 密钥保管库是一种基于云的服务，可帮助保护应用和服务使用的加密密钥和机密。 将 Azure 密钥保管库用于 ASP.NET 核心应用的常见方案包括：

* 控制对敏感配置数据的访问。
* 在存储配置数据时满足 FIPS 140-2 级验证的硬件安全模块 （HSM） 的要求。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="packages"></a>包

向[Microsoft.扩展.配置.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包添加包引用。

## <a name="sample-app"></a>示例应用

示例应用以`#define`*Program.cs*文件顶部的语句确定的两种模式之一运行：

* `Certificate`&ndash;演示使用 Azure 密钥保管库客户端 ID 和 X.509 证书访问存储在 Azure 密钥保管库中的秘密。 此版本的示例可以从任何位置运行，部署到 Azure 应用服务或任何能够为 ASP.NET 酷应用提供服务的主机。
* `Managed`&ndash;演示如何[使用 Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)使用 Azure AD 身份验证将应用验证为 Azure 密钥保管库，而无需在应用的代码或配置中存储凭据。 使用托管标识进行身份验证时，不需要 Azure AD 应用程序 ID 和密码（客户端密钥）。 必须`Managed`将示例的版本部署到 Azure。 按照"[为 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)"部分中的指南进行操作。

有关如何使用预处理器指令 （）`#define`配置示例应用的详细信息，请参阅<xref:index#preprocessor-directives-in-sample-code>。

## <a name="secret-storage-in-the-development-environment"></a>开发环境中的秘密存储

使用[机密管理器工具](xref:security/app-secrets)在本地设置机密。 当示例应用在开发环境中的本地计算机上运行时，将从本地机密管理器存储加载机密。

"机密管理器"工具需要`<UserSecretsId>`应用的项目文件中的属性。 将属性值 （`{GUID}`） 设置为任何唯一的 GUID：

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

机密创建为名称值对。 层次结构值（配置部分）在`:`[ASP.NET核心配置](xref:fundamentals/configuration/index)密钥名称中使用（冒号）作为分隔符。

机密管理器从打开到项目[内容根](xref:fundamentals/index#content-root)的命令外壳使用，其中`{SECRET NAME}`的名称和`{SECRET VALUE}`值是：

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

从项目[的内容根](xref:fundamentals/index#content-root)在命令 shell 中执行以下命令，以设置示例应用的秘密：

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

当这些机密存储在[Azure 密钥保管库部分的生产环境中的"机密存储"中的](#secret-storage-in-the-production-environment-with-azure-key-vault)Azure 密钥保管`_dev`库中时，后缀将`_prod`更改为 。 后缀在应用的输出中提供指示配置值来源的可视提示。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>使用 Azure 密钥保管库的生产环境中的秘密存储

此处汇总了["快速入门：使用 Azure CLI"主题从 Azure 密钥保管库设置和检索机密](/azure/key-vault/quick-create-cli)的说明，用于创建 Azure 密钥保管库并存储示例应用使用的秘密。 有关详细信息，请参阅主题。

1. 使用[Azure 门户](https://portal.azure.com/)中的以下任一方法打开 Azure 云外壳：

   * 选择代码块右上角的“试用”。**** 在文本框中使用搜索字符串"Azure CLI"。
   * 使用**启动云外壳**按钮在浏览器中打开云外壳。
   * 选择 Azure 门户右上角菜单上的 **"云壳**"按钮。

   有关详细信息，请参阅[Azure CLI](/cli/azure/)和[Azure 云外壳概述](/azure/cloud-shell/overview)。

1. 如果您尚未通过身份验证，请使用 命令`az login`登录。

1. 使用以下命令创建资源组，其中`{RESOURCE GROUP NAME}`为新资源组的资源组名称为`{LOCATION}`Azure 区域（数据中心）：

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 使用以下命令在资源组中创建密钥保管库，其中`{KEY VAULT NAME}`为新密钥保管库的名称，`{LOCATION}`是 Azure 区域（数据中心）：

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 在密钥保管库中创建机密作为名称值对。

   Azure 密钥保管库密钥库密钥名称仅限于字母数字字符和破折号。 分层值（配置部分）使用`--`（两个破折号）作为分隔符。 在密钥保管库密钥名称中，通常不允许从[ASP.NET Core 配置](xref:fundamentals/configuration/index)中从子键中分隔节的冒号。 因此，当机密加载到应用的配置中时，将使用两个破折号并将其交换为冒号。

   以下机密用于示例应用。 这些值包括后`_prod`缀，用于将它们与在开发环境中`_dev`加载的后缀值与用户机密区分开来。 替换为`{KEY VAULT NAME}`在上一步骤中创建的密钥保管库的名称：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>对非 Azure 托管应用使用应用程序 ID 和 X.509 证书

将 Azure AD、Azure 密钥保管库和应用配置为使用 Azure 活动目录应用程序 ID 和 X.509 证书在**应用托管在 Azure 之外时**对密钥保管库进行身份验证。 有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。

> [!NOTE]
> 尽管 Azure 中托管的应用支持使用应用程序 ID 和 X.509 证书，但我们建议在 Azure 中托管应用时[对 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)。 托管标识不需要在应用或开发环境中存储证书。

当*Program.cs*文件顶部的语句设置为`Certificate`时，`#define`示例应用使用应用程序 ID 和 X.509 证书。

1. 创建 PKCS_12 存档 （*.pfx*） 证书。 创建证书的选项包括[Windows 上的 MakeCert](/windows/desktop/seccrypto/makecert)和[OpenSSL](https://www.openssl.org/)。
1. 将证书安装到当前用户的个人证书存储中。 将密钥标记为可导出是可选的。 请注意证书的指纹，此过程稍后将使用。
1. 将 PKCS_12 存档 *（.pfx*） 证书导出为 DER 编码证书 *（.cer*）。
1. 使用 Azure AD（**应用注册）注册**应用。
1. 将 DER 编码的证书 *（.cer*） 上载到 Azure AD：
   1. 在 Azure AD 中选择应用。
   1. 导航到**证书&机密**。
   1. 选择 **"上载证书**"以上载包含公钥的证书。 *.cer* *、.pem*或 *.crt*证书是可以接受的。
1. 将密钥保管库名称、应用程序 ID 和证书指纹存储在应用*的应用设置.json*文件中。
1. 导航到 Azure 门户中的**密钥保管库**。
1. [使用 Azure 密钥保管库部分在"生产环境"中的秘密存储中选择在"机密存储"中](#secret-storage-in-the-production-environment-with-azure-key-vault)创建的密钥保管库。
1. 选择**访问策略**。
1. 选择 **"添加访问策略**"。
1. 打开 **"机密"权限**，并提供具有**获取**和**列表**权限的应用。
1. 选择 **"选择主体**"并按名称选择已注册的应用。 选择“选择”按钮  。
1. 选择“确定”  。
1. 选择“保存”。 
1. 部署应用。

示例`Certificate`应用从`IConfigurationRoot`与机密名称同名获取其配置值：

* 非分层值：使用 获取`SecretName`的值`config["SecretName"]`。
* 分层值（节）：使用`:`（冒号）表示法或`GetSection`扩展方法。 使用以下任一方法获取配置值：
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 证书由操作系统管理。 应用调用<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>应用*设置.json*文件提供的值：

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

示例值：

* 密钥保管库名称：`contosovault`
* 应用程序 ID：`627e911e-43cc-61d4-992e-12db9c81b413`
* 证书指纹：`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*应用程序设置.json*：

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

运行应用时，网页将显示加载的机密值。 在开发环境中，机密值随后缀一`_dev`起加载。 在生产环境中，值使用`_prod`后缀加载。

## <a name="use-managed-identities-for-azure-resources"></a>对 Azure 资源使用托管标识

**部署到 Azure 的应用**可以利用 Azure[资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure 密钥保管库进行身份验证，而无需在应用中存储凭据（应用程序 ID 和密码/客户端密钥）。

当*Program.cs*文件顶部的语句设置为`Managed`时，`#define`示例应用对 Azure 资源使用托管标识。

在应用*的应用设置.json*文件中输入保管库名称。 当示例应用设置为`Managed`版本时，不需要应用程序 ID 和密码（客户端密钥），因此您可以忽略这些配置条目。 应用将部署到 Azure，Azure 会验证应用仅使用存储在*appsettings.json*文件中的保管库名称访问 Azure 密钥保管库。

将示例应用部署到 Azure 应用服务。

创建服务时，部署到 Azure 应用服务的应用将自动注册到 Azure AD。 从部署中获取对象 ID，以便在以下命令中使用。 对象 ID 显示在应用服务的 **"标识"** 面板上的 Azure 门户中。

使用 Azure CLI 和应用的对象 ID，向应用提供`list`访问`get`密钥保管库的权限：

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

使用 Azure CLI、PowerShell 或 Azure 门户**重新启动应用**。

示例应用：

* 创建没有连接字符串的`AzureServiceTokenProvider`类实例。 未提供连接字符串时，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用`AzureServiceTokenProvider`实例令牌回调创建新。
* 实例<xref:Microsoft.Azure.KeyVault.KeyVaultClient>与加载所有机密值的默认实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>一起使用，并将双分线 （）`--`替换为键名中的冒号`:`（ ）。

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

密钥保管库名称示例值：`contosovault`
    
*应用程序设置.json*：

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

运行应用时，网页将显示加载的机密值。 在开发环境中，机密值具有后缀`_dev`，因为它们由用户机密提供。 在生产环境中，值使用后缀加载，`_prod`因为它们由 Azure 密钥保管库提供。

如果收到错误`Access denied`，请确认应用已注册 Azure AD 并提供有关密钥保管库的访问权限。 确认已重新启动 Azure 中的服务。

有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅创建 Azure[资源管理器服务连接到具有托管服务标识的 VM。](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="configuration-options"></a>配置选项

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>可以接受 ： <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Property         | 说明 |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>用于检索值。 |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>用于控制机密加载的实例。 |
| `ReloadInterval` | `Timespan`在轮询密钥保管库以进行更改的尝试之间等待。 默认值为`null`（未重新加载配置）。 |
| `Vault`          | 密钥保管库 URI。 |

## <a name="use-a-key-name-prefix"></a>使用键名称前缀

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供接受 的实现的<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>重载，它允许您控制如何将密钥保管库机密转换为配置密钥。 例如，您可以实现接口，以基于您在应用启动时提供的前缀值加载机密值。 例如，这允许您根据应用版本加载机密。

> [!WARNING]
> 不要使用密钥保管库机密的前缀将多个应用的机密放入同一密钥保管库或将环境机密（例如 *，开发*与*生产*机密）放入同一保管库。 我们建议不同的应用和开发/生产环境使用单独的密钥保管库来隔离应用环境，以确保最高级别的安全性。

在下面的示例中，在密钥保管库中建立了机密（并使用开发环境的密钥管理器工具）（`5000-AppSecret`密钥保管库机密名称中不允许周期）。 此机密表示应用版本 5.0.0.0 的应用机密。 对于应用的另一个版本 5.1.0.0，将机密添加到 的密钥保管库（并使用机密管理器工具）中`5100-AppSecret`。 每个应用版本将其版本控制的秘密值加载到其配置中`AppSecret`，作为 ，在加载密钥时剥离版本。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>使用自定义<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>调用 ：

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>对机密的版本前缀做出反应，以将正确的机密加载到配置中：

* `Load`当机密的名称以前缀开头时加载它。 其他机密未加载。
* `GetKey`:
  * 从机密名称中删除前缀。
  * 将任何名称中的两个破折号替换为`KeyDelimiter`， 是 配置中使用的分隔符（通常是冒号）。 Azure 密钥保管库不允许在机密名称中冒号。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

该方法`Load`由提供程序算法调用，该算法通过保管库机密进行遍舍以查找具有版本前缀的提供程序算法。 使用 找到`Load`版本前缀时，算法使用 方法`GetKey`返回机密名称的配置名称。 它将版本前缀从机密的名称中剥离，并返回用于加载到应用的配置名称值对中的其他机密名称。

实施此方法时：

1. 应用在应用的项目文件中指定的版本。 在下面的示例中，应用的版本设置为`5.0.0.0`：

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. 确认应用的项目`<UserSecretsId>`文件中存在属性，其中`{GUID}`是用户提供的 GUID：

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   使用[秘密管理器工具](xref:security/app-secrets)在本地保存以下机密：

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 机密使用以下 Azure CLI 命令保存在 Azure 密钥保管库中：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 运行应用时，将加载密钥保管库机密。 的`5000-AppSecret`字符串机密与应用的项目文件 （）`5.0.0.0`中指定的应用版本匹配。

1. 版本`5000`（使用破折号）从密钥名称中剥离。 在整个应用中，使用密钥`AppSecret`读取配置将加载机密值。

1. 如果在项目文件中将应用的版本更改为，`5.1.0.0`并且应用再次运行，则返回的机密值位于`5.1.0.0_secret_value_dev`"开发"环境和`5.1.0.0_secret_value_prod`"生产"中。

> [!NOTE]
> 您还可以向<xref:Microsoft.Azure.KeyVault.KeyVaultClient><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供您自己的实现。 自定义客户端允许跨应用共享客户端的单个实例。

## <a name="bind-an-array-to-a-class"></a>将数组绑定至类

提供程序能够将配置值读取到数组中以绑定到 POCO 数组。

从允许键包含冒号`:`（） 分隔符的配置源读取时，使用数字键段来区分组成数组 （`:0:`的`:1:`&hellip;`:{n}:`键 ） 。 有关详细信息，请参阅[配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。

Azure 密钥保管库密钥不能将冒号用作分隔符。 本主题中描述的方法使用双破折号 （）`--`作为分层值（节）的分隔符。 数组键存储在 Azure 密钥保管库中，具有双破折号和数字键段`--0--` `--1--`（， &hellip; `--{n}--`。 。

检查 JSON 文件提供的以下[Serilog](https://serilog.net/)日志记录提供程序配置。 `WriteTo`数组中定义了两个对象文本，反映了两个 Serilog*接收器*，它们描述日志记录输出的目标：

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

前面 JSON 文件中显示的配置使用双破折号 （）`--`表示法和数字段存储在 Azure 密钥保管库中：

| 密钥 | 值 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>重新加载机密

机密被缓存，直到`IConfigurationRoot.Reload()`被调用。 在执行密钥保管库之前`Reload`，应用不会尊重密钥保管库中过期、禁用和更新的机密。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>已禁用和过期的机密

禁用和过期的机密引发<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>。 要防止应用引发，请使用其他配置提供程序提供配置或更新禁用或过期的机密。

## <a name="troubleshoot"></a>疑难解答

当应用无法使用提供程序加载配置时，将写入[ASP.NET核心日志记录基础结构](xref:fundamentals/logging/index)中的错误消息。 以下条件将阻止加载配置：

* 应用或证书未在 Azure 活动目录中正确配置。
* 密钥保管库在 Azure 密钥保管库中不存在。
* 应用程序无权访问密钥保管库。
* 访问策略不包括`Get`和`List`权限。
* 在密钥保管库中，配置数据（名称值对）被错误地命名、丢失、禁用或过期。
* 应用具有错误的密钥保管库名称 （`KeyVaultName`）、Azure AD`AzureADApplicationId`应用程序 ID （） 或`AzureADCertThumbprint`Azure AD 证书指纹 （）。
* 对于要加载的值，配置键（名称）在应用中不正确。
* 将应用的访问策略添加到密钥保管库时，将创建该策略，但在**Access 策略**UI 中未选择 **"保存**"按钮。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/configuration/index>
* [微软 Azure：密钥保管库](https://azure.microsoft.com/services/key-vault/)
* [微软 Azure：密钥保管库文档](/azure/key-vault/)
* [如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥](/azure/key-vault/key-vault-hsm-protected-keys)
* [密钥库客户端类](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密](/azure/key-vault/quick-create-net)
* [教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文档介绍如何使用[Microsoft Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)配置提供程序从 Azure 密钥保管库机密加载应用配置值。 Azure 密钥保管库是一种基于云的服务，可帮助保护应用和服务使用的加密密钥和机密。 将 Azure 密钥保管库用于 ASP.NET 核心应用的常见方案包括：

* 控制对敏感配置数据的访问。
* 在存储配置数据时满足 FIPS 140-2 级验证的硬件安全模块 （HSM） 的要求。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="packages"></a>包

向[Microsoft.扩展.配置.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)包添加包引用。

## <a name="sample-app"></a>示例应用

示例应用以`#define`*Program.cs*文件顶部的语句确定的两种模式之一运行：

* `Certificate`&ndash;演示使用 Azure 密钥保管库客户端 ID 和 X.509 证书访问存储在 Azure 密钥保管库中的秘密。 此版本的示例可以从任何位置运行，部署到 Azure 应用服务或任何能够为 ASP.NET 酷应用提供服务的主机。
* `Managed`&ndash;演示如何[使用 Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)使用 Azure AD 身份验证将应用验证为 Azure 密钥保管库，而无需在应用的代码或配置中存储凭据。 使用托管标识进行身份验证时，不需要 Azure AD 应用程序 ID 和密码（客户端密钥）。 必须`Managed`将示例的版本部署到 Azure。 按照"[为 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)"部分中的指南进行操作。

有关如何使用预处理器指令 （）`#define`配置示例应用的详细信息，请参阅<xref:index#preprocessor-directives-in-sample-code>。

## <a name="secret-storage-in-the-development-environment"></a>开发环境中的秘密存储

使用[机密管理器工具](xref:security/app-secrets)在本地设置机密。 当示例应用在开发环境中的本地计算机上运行时，将从本地机密管理器存储加载机密。

"机密管理器"工具需要`<UserSecretsId>`应用的项目文件中的属性。 将属性值 （`{GUID}`） 设置为任何唯一的 GUID：

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

机密创建为名称值对。 层次结构值（配置部分）在`:`[ASP.NET核心配置](xref:fundamentals/configuration/index)密钥名称中使用（冒号）作为分隔符。

机密管理器从打开到项目[内容根](xref:fundamentals/index#content-root)的命令外壳使用，其中`{SECRET NAME}`的名称和`{SECRET VALUE}`值是：

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

从项目[的内容根](xref:fundamentals/index#content-root)在命令 shell 中执行以下命令，以设置示例应用的秘密：

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

当这些机密存储在[Azure 密钥保管库部分的生产环境中的"机密存储"中的](#secret-storage-in-the-production-environment-with-azure-key-vault)Azure 密钥保管`_dev`库中时，后缀将`_prod`更改为 。 后缀在应用的输出中提供指示配置值来源的可视提示。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>使用 Azure 密钥保管库的生产环境中的秘密存储

此处汇总了["快速入门：使用 Azure CLI"主题从 Azure 密钥保管库设置和检索机密](/azure/key-vault/quick-create-cli)的说明，用于创建 Azure 密钥保管库并存储示例应用使用的秘密。 有关详细信息，请参阅主题。

1. 使用[Azure 门户](https://portal.azure.com/)中的以下任一方法打开 Azure 云外壳：

   * 选择代码块右上角的“试用”。**** 在文本框中使用搜索字符串"Azure CLI"。
   * 使用**启动云外壳**按钮在浏览器中打开云外壳。
   * 选择 Azure 门户右上角菜单上的 **"云壳**"按钮。

   有关详细信息，请参阅[Azure CLI](/cli/azure/)和[Azure 云外壳概述](/azure/cloud-shell/overview)。

1. 如果您尚未通过身份验证，请使用 命令`az login`登录。

1. 使用以下命令创建资源组，其中`{RESOURCE GROUP NAME}`为新资源组的资源组名称为`{LOCATION}`Azure 区域（数据中心）：

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 使用以下命令在资源组中创建密钥保管库，其中`{KEY VAULT NAME}`为新密钥保管库的名称，`{LOCATION}`是 Azure 区域（数据中心）：

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 在密钥保管库中创建机密作为名称值对。

   Azure 密钥保管库密钥库密钥名称仅限于字母数字字符和破折号。 分层值（配置部分）使用`--`（两个破折号）作为分隔符。 在密钥保管库密钥名称中，通常不允许从[ASP.NET Core 配置](xref:fundamentals/configuration/index)中从子键中分隔节的冒号。 因此，当机密加载到应用的配置中时，将使用两个破折号并将其交换为冒号。

   以下机密用于示例应用。 这些值包括后`_prod`缀，用于将它们与在开发环境中`_dev`加载的后缀值与用户机密区分开来。 替换为`{KEY VAULT NAME}`在上一步骤中创建的密钥保管库的名称：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>对非 Azure 托管应用使用应用程序 ID 和 X.509 证书

将 Azure AD、Azure 密钥保管库和应用配置为使用 Azure 活动目录应用程序 ID 和 X.509 证书在**应用托管在 Azure 之外时**对密钥保管库进行身份验证。 有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。

> [!NOTE]
> 尽管 Azure 中托管的应用支持使用应用程序 ID 和 X.509 证书，但我们建议在 Azure 中托管应用时[对 Azure 资源使用托管标识](#use-managed-identities-for-azure-resources)。 托管标识不需要在应用或开发环境中存储证书。

当*Program.cs*文件顶部的语句设置为`Certificate`时，`#define`示例应用使用应用程序 ID 和 X.509 证书。

1. 创建 PKCS_12 存档 （*.pfx*） 证书。 创建证书的选项包括[Windows 上的 MakeCert](/windows/desktop/seccrypto/makecert)和[OpenSSL](https://www.openssl.org/)。
1. 将证书安装到当前用户的个人证书存储中。 将密钥标记为可导出是可选的。 请注意证书的指纹，此过程稍后将使用。
1. 将 PKCS_12 存档 *（.pfx*） 证书导出为 DER 编码证书 *（.cer*）。
1. 使用 Azure AD（**应用注册）注册**应用。
1. 将 DER 编码的证书 *（.cer*） 上载到 Azure AD：
   1. 在 Azure AD 中选择应用。
   1. 导航到**证书&机密**。
   1. 选择 **"上载证书**"以上载包含公钥的证书。 *.cer* *、.pem*或 *.crt*证书是可以接受的。
1. 将密钥保管库名称、应用程序 ID 和证书指纹存储在应用*的应用设置.json*文件中。
1. 导航到 Azure 门户中的**密钥保管库**。
1. [使用 Azure 密钥保管库部分在"生产环境"中的秘密存储中选择在"机密存储"中](#secret-storage-in-the-production-environment-with-azure-key-vault)创建的密钥保管库。
1. 选择**访问策略**。
1. 选择 **"添加访问策略**"。
1. 打开 **"机密"权限**，并提供具有**获取**和**列表**权限的应用。
1. 选择 **"选择主体**"并按名称选择已注册的应用。 选择“选择”按钮  。
1. 选择“确定”  。
1. 选择“保存”。 
1. 部署应用。

示例`Certificate`应用从`IConfigurationRoot`与机密名称同名获取其配置值：

* 非分层值：使用 获取`SecretName`的值`config["SecretName"]`。
* 分层值（节）：使用`:`（冒号）表示法或`GetSection`扩展方法。 使用以下任一方法获取配置值：
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 证书由操作系统管理。 应用调用<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>应用*设置.json*文件提供的值：

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

示例值：

* 密钥保管库名称：`contosovault`
* 应用程序 ID：`627e911e-43cc-61d4-992e-12db9c81b413`
* 证书指纹：`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*应用程序设置.json*：

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

运行应用时，网页将显示加载的机密值。 在开发环境中，机密值随后缀一`_dev`起加载。 在生产环境中，值使用`_prod`后缀加载。

## <a name="use-managed-identities-for-azure-resources"></a>对 Azure 资源使用托管标识

**部署到 Azure 的应用**可以利用 Azure[资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure 密钥保管库进行身份验证，而无需在应用中存储凭据（应用程序 ID 和密码/客户端密钥）。

当*Program.cs*文件顶部的语句设置为`Managed`时，`#define`示例应用对 Azure 资源使用托管标识。

在应用*的应用设置.json*文件中输入保管库名称。 当示例应用设置为`Managed`版本时，不需要应用程序 ID 和密码（客户端密钥），因此您可以忽略这些配置条目。 应用将部署到 Azure，Azure 会验证应用仅使用存储在*appsettings.json*文件中的保管库名称访问 Azure 密钥保管库。

将示例应用部署到 Azure 应用服务。

创建服务时，部署到 Azure 应用服务的应用将自动注册到 Azure AD。 从部署中获取对象 ID，以便在以下命令中使用。 对象 ID 显示在应用服务的 **"标识"** 面板上的 Azure 门户中。

使用 Azure CLI 和应用的对象 ID，向应用提供`list`访问`get`密钥保管库的权限：

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

使用 Azure CLI、PowerShell 或 Azure 门户**重新启动应用**。

示例应用：

* 创建没有连接字符串的`AzureServiceTokenProvider`类实例。 未提供连接字符串时，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用`AzureServiceTokenProvider`实例令牌回调创建新。
* 实例<xref:Microsoft.Azure.KeyVault.KeyVaultClient>与加载所有机密值的默认实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>一起使用，并将双分线 （）`--`替换为键名中的冒号`:`（ ）。

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

密钥保管库名称示例值：`contosovault`
    
*应用程序设置.json*：

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

运行应用时，网页将显示加载的机密值。 在开发环境中，机密值具有后缀`_dev`，因为它们由用户机密提供。 在生产环境中，值使用后缀加载，`_prod`因为它们由 Azure 密钥保管库提供。

如果收到错误`Access denied`，请确认应用已注册 Azure AD 并提供有关密钥保管库的访问权限。 确认已重新启动 Azure 中的服务。

有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅创建 Azure[资源管理器服务连接到具有托管服务标识的 VM。](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="use-a-key-name-prefix"></a>使用键名称前缀

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供接受 的实现的<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>重载，它允许您控制如何将密钥保管库机密转换为配置密钥。 例如，您可以实现接口，以基于您在应用启动时提供的前缀值加载机密值。 例如，这允许您根据应用版本加载机密。

> [!WARNING]
> 不要使用密钥保管库机密的前缀将多个应用的机密放入同一密钥保管库或将环境机密（例如 *，开发*与*生产*机密）放入同一保管库。 我们建议不同的应用和开发/生产环境使用单独的密钥保管库来隔离应用环境，以确保最高级别的安全性。

在下面的示例中，在密钥保管库中建立了机密（并使用开发环境的密钥管理器工具）（`5000-AppSecret`密钥保管库机密名称中不允许周期）。 此机密表示应用版本 5.0.0.0 的应用机密。 对于应用的另一个版本 5.1.0.0，将机密添加到 的密钥保管库（并使用机密管理器工具）中`5100-AppSecret`。 每个应用版本将其版本控制的秘密值加载到其配置中`AppSecret`，作为 ，在加载密钥时剥离版本。

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>使用自定义<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>调用 ：

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

实现<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>对机密的版本前缀做出反应，以将正确的机密加载到配置中：

* `Load`当机密的名称以前缀开头时加载它。 其他机密未加载。
* `GetKey`:
  * 从机密名称中删除前缀。
  * 将任何名称中的两个破折号替换为`KeyDelimiter`， 是 配置中使用的分隔符（通常是冒号）。 Azure 密钥保管库不允许在机密名称中冒号。

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

该方法`Load`由提供程序算法调用，该算法通过保管库机密进行遍舍以查找具有版本前缀的提供程序算法。 使用 找到`Load`版本前缀时，算法使用 方法`GetKey`返回机密名称的配置名称。 它将版本前缀从机密的名称中剥离，并返回用于加载到应用的配置名称值对中的其他机密名称。

实施此方法时：

1. 应用在应用的项目文件中指定的版本。 在下面的示例中，应用的版本设置为`5.0.0.0`：

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. 确认应用的项目`<UserSecretsId>`文件中存在属性，其中`{GUID}`是用户提供的 GUID：

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   使用[秘密管理器工具](xref:security/app-secrets)在本地保存以下机密：

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 机密使用以下 Azure CLI 命令保存在 Azure 密钥保管库中：

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 运行应用时，将加载密钥保管库机密。 的`5000-AppSecret`字符串机密与应用的项目文件 （）`5.0.0.0`中指定的应用版本匹配。

1. 版本`5000`（使用破折号）从密钥名称中剥离。 在整个应用中，使用密钥`AppSecret`读取配置将加载机密值。

1. 如果在项目文件中将应用的版本更改为，`5.1.0.0`并且应用再次运行，则返回的机密值位于`5.1.0.0_secret_value_dev`"开发"环境和`5.1.0.0_secret_value_prod`"生产"中。

> [!NOTE]
> 您还可以向<xref:Microsoft.Azure.KeyVault.KeyVaultClient><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>提供您自己的实现。 自定义客户端允许跨应用共享客户端的单个实例。

## <a name="bind-an-array-to-a-class"></a>将数组绑定至类

提供程序能够将配置值读取到数组中以绑定到 POCO 数组。

从允许键包含冒号`:`（） 分隔符的配置源读取时，使用数字键段来区分组成数组 （`:0:`的`:1:`&hellip;`:{n}:`键 ） 。 有关详细信息，请参阅[配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。

Azure 密钥保管库密钥不能将冒号用作分隔符。 本主题中描述的方法使用双破折号 （）`--`作为分层值（节）的分隔符。 数组键存储在 Azure 密钥保管库中，具有双破折号和数字键段`--0--` `--1--`（， &hellip; `--{n}--`。 。

检查 JSON 文件提供的以下[Serilog](https://serilog.net/)日志记录提供程序配置。 `WriteTo`数组中定义了两个对象文本，反映了两个 Serilog*接收器*，它们描述日志记录输出的目标：

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

前面 JSON 文件中显示的配置使用双破折号 （）`--`表示法和数字段存储在 Azure 密钥保管库中：

| 密钥 | 值 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>重新加载机密

机密被缓存，直到`IConfigurationRoot.Reload()`被调用。 在执行密钥保管库之前`Reload`，应用不会尊重密钥保管库中过期、禁用和更新的机密。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>已禁用和过期的机密

禁用和过期的机密引发<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>。 要防止应用引发，请使用其他配置提供程序提供配置或更新禁用或过期的机密。

## <a name="troubleshoot"></a>疑难解答

当应用无法使用提供程序加载配置时，将写入[ASP.NET核心日志记录基础结构](xref:fundamentals/logging/index)中的错误消息。 以下条件将阻止加载配置：

* 应用或证书未在 Azure 活动目录中正确配置。
* 密钥保管库在 Azure 密钥保管库中不存在。
* 应用程序无权访问密钥保管库。
* 访问策略不包括`Get`和`List`权限。
* 在密钥保管库中，配置数据（名称值对）被错误地命名、丢失、禁用或过期。
* 应用具有错误的密钥保管库名称 （`KeyVaultName`）、Azure AD`AzureADApplicationId`应用程序 ID （） 或`AzureADCertThumbprint`Azure AD 证书指纹 （）。
* 对于要加载的值，配置键（名称）在应用中不正确。
* 将应用的访问策略添加到密钥保管库时，将创建该策略，但在**Access 策略**UI 中未选择 **"保存**"按钮。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/configuration/index>
* [微软 Azure：密钥保管库](https://azure.microsoft.com/services/key-vault/)
* [微软 Azure：密钥保管库文档](/azure/key-vault/)
* [如何为 Azure 密钥保管库生成和传输受 HSM 保护的密钥](/azure/key-vault/key-vault-hsm-protected-keys)
* [密钥库客户端类](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密](/azure/key-vault/quick-create-net)
* [教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

