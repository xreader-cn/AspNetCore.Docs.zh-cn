---
title: ASP.NET Core 中的密钥存储提供程序
author: rick-anderson
description: 了解 ASP.NET Core 中的密钥存储提供程序以及如何配置密钥存储位置。
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: ec746f383c18ccc7b60c614c990f7577d2d52a20
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74052849"
---
# <a name="key-storage-providers-in-aspnet-core"></a>ASP.NET Core 中的密钥存储提供程序

默认情况下，数据保护系统[使用发现机制](xref:security/data-protection/configuration/default-settings)来确定应在何处保存加密密钥。 开发人员可以重写默认发现机制并手动指定位置。

> [!WARNING]
> 如果指定显式密钥持久性位置，数据保护系统将注销静态密钥加密机制，这样密钥就不再静态加密。 建议你另外指定用于生产部署的[显式密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)。

## <a name="file-system"></a>文件系统

若要配置基于文件系统的密钥存储库，请调用[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)配置例程，如下所示。 提供指向存储密钥的存储库的[DirectoryInfo](/dotnet/api/system.io.directoryinfo) ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo` 可以指向本地计算机上的目录，也可以指向网络共享上的文件夹。 如果指向本地计算机上的目录（并且方案为仅本地计算机上的应用需要使用此存储库的访问权限），请考虑使用[WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) （在 windows 上）对静态密钥进行加密。 否则，请考虑使用[x.509 证书](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。

## <a name="azure-storage"></a>Azure 存储空间

[AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package 允许将数据保护密钥存储在 Azure Blob 存储中。 可在 web 应用的多个实例之间共享密钥。 应用可在多个服务器之间共享身份验证 cookie 或 CSRF 保护。

若要配置 Azure Blob 存储提供程序，请调用[PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)重载之一。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

如果 web 应用作为 Azure 服务运行，则可以使用[microsoft.azure.services.appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)自动创建身份验证令牌。

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

查看[有关配置服务到服务身份验证的更多详细信息。](/azure/key-vault/service-to-service-authentication)

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

[AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package 允许在 Redis 缓存中存储数据保护密钥。 可在 web 应用的多个实例之间共享密钥。 应用可在多个服务器之间共享身份验证 cookie 或 CSRF 保护。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[AspNetCore. DataProtection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package 允许在 Redis 缓存中存储数据保护密钥。 可在 web 应用的多个实例之间共享密钥。 应用可在多个服务器之间共享身份验证 cookie 或 CSRF 保护。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

若要在 Redis 上进行配置，请调用[PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis)重载之一：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

若要在 Redis 上进行配置，请调用[PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis)重载之一：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

有关更多信息，请参见下列主题：

* [Stackexchange.redis. Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis 缓存](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [aspnet/DataProtection 示例](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>注册表

**仅适用于 Windows 部署。**

有时应用程序可能没有对文件系统的写访问权限。 请考虑一个应用以虚拟服务帐户（例如*w3wp.exe*的应用程序池标识）运行的方案。 在这些情况下，管理员可以设置可由服务帐户标识访问的注册表项。 调用[PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry)扩展方法，如下所示。 提供一个[RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) ，指向应存储加密密钥的位置：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> 建议使用[WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[AspNetCore. DataProtection. microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)包提供了一种机制，用于使用 Entity Framework Core 将数据保护密钥存储到数据库。 必须将 `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet 包添加到项目文件中，它不是[AspNetCore 元包](xref:fundamentals/metapackage-app)的一部分。

使用此包，可以在 web 应用的多个实例之间共享密钥。

若要配置 EF Core 提供程序，请调用[`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)方法：

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

泛型参数 `TContext`必须继承自[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)并实现[IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)：

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

创建 `DataProtectionKeys` 表。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在 "**包管理器控制台**" （PMC）窗口中执行以下命令：

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

在命令行界面中执行以下命令：

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` 是前面的代码示例中定义的 `DbContext`。 如果你使用的是其他名称的 `DbContext`，请将 `DbContext` 名称替换为 `MyKeysContext`。

`DataProtectionKeys` 类/实体采用下表所示的结构。

| 属性/字段 | CLR 类型 | SQL 类型              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`，PK，not null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`，null |
| `Xml`          | `string` | `nvarchar(MAX)`，null |

::: moniker-end

## <a name="custom-key-repository"></a>自定义密钥存储库

如果不适合使用机箱内机制，开发人员可以通过提供自定义[IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)来指定其自己的密钥持久性机制。
