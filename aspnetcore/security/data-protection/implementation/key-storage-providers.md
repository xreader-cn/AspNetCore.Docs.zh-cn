---
title: 在 ASP.NET Core 中的密钥存储提供程序
author: rick-anderson
description: 了解有关 ASP.NET Core 以及如何配置密钥的存储位置中的密钥存储提供程序。
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 49b068480fe7ba0a9b338aa5f5b7fc19fb98528f
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048098"
---
# <a name="key-storage-providers-in-aspnet-core"></a>在 ASP.NET Core 中的密钥存储提供程序

数据保护系统[默认情况下使用的发现机制](xref:security/data-protection/configuration/default-settings)来确定应该保留加密密钥的位置。 开发人员可以重写默认的发现机制，并手动指定的位置。

> [!WARNING]
> 如果指定了显式密钥持久性位置，数据保护系统注销 rest 机制，在默认密钥加密，因此不再静态加密密钥。 我们建议你此外[指定一个显式的密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)对于生产部署。

## <a name="file-system"></a>文件系统

若要配置的文件基于系统的密钥存储库，请调用[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)配置例程，如下所示。 提供[DirectoryInfo](/dotnet/api/system.io.directoryinfo)指向存储密钥的存储库：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`可以指向本地计算机上的某个目录，也可以指向网络共享上的文件夹。 如果指向本地计算机上的目录 （和的方案是只在本地计算机上的应用需要使用此存储库的访问权限），请考虑使用[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (在 Windows) 来加密静态密钥。 否则，请考虑使用[X.509 证书](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。

## <a name="azure-storage"></a>Azure 存储

[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)包允许在 Azure Blob 存储中存储数据保护密钥。 可以在 web 应用的多个实例之间共享密钥。 应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。

若要配置 Azure Blob 存储提供程序，请调用之一[PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)重载。 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

如果 web 应用正在作为一项 Azure 服务，身份验证令牌可以自动使用创建[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)。 

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

请参阅[有关配置服务到服务身份验证的更多详细信息。](/azure/key-vault/service-to-service-authentication)

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

[Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/)包允许将数据保护密钥存储在 Redis 缓存。 可以在 web 应用的多个实例之间共享密钥。 应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/)包允许将数据保护密钥存储在 Redis 缓存。 可以在 web 应用的多个实例之间共享密钥。 应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

若要配置 Redis，调用之一[PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis)重载：

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

若要配置 Redis，调用之一[PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis)重载：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

有关详细信息，请参阅下列主题：

* [StackExchange.Redis ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis 缓存](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [aspnet/DataProtection 示例](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>注册表

**仅适用于 Windows 的部署。**

有时应用程序可能没有到文件系统的写访问权限。 请考虑应用正在作为虚拟服务帐户的方案 (如*w3wp.exe*的应用程序池标识)。 在这些情况下，管理员可以预配的服务帐户标识都可访问的注册表项。 调用[PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry)扩展方法，如下所示。 提供[RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey)指向存储加密密钥的位置：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> 我们建议使用[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)包提供了用于存储到使用 Entity Framework Core 的数据库的数据保护密钥的机制。 `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet 包必须将添加到项目文件中，它不属于[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。

通过此包，可以在 web 应用的多个实例之间共享密钥。

若要配置 EF Core 提供程序，请调用[ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)方法：

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

泛型参数`TContext`，必须继承自[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) ，并实现[IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

创建`DataProtectionKeys`表。 

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

执行以下命令中的**程序包管理器控制台**(PMC) 窗口：

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

在命令行界面中执行以下命令：

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` 是`DbContext`在上面的代码示例中定义。 如果您使用的`DbContext`使用不同的名称，替换为你`DbContext`名称`MyKeysContext`。

`DataProtectionKeys`类/实体采用下表中所示的结构。

| 属性/字段 | CLR 类型 | SQL 类型              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`PK，不为 null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`null |
| `Xml`          | `string` | `nvarchar(MAX)`null |

::: moniker-end

## <a name="custom-key-repository"></a>自定义密钥存储库

如果不适当的内置机制，开发人员可以通过提供自定义指定自己的密钥持久性机制[IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)。
