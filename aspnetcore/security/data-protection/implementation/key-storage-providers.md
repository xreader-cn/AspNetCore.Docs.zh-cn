---
title: ASP.NET Core 中的密钥存储提供程序
author: rick-anderson
description: 了解 ASP.NET Core 中的密钥存储提供程序以及如何配置密钥存储位置。
ms.author: riande
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 36e8bc494125d0770347ddf32390365d83a91d27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051741"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="c2c17-103">ASP.NET Core 中的密钥存储提供程序</span><span class="sxs-lookup"><span data-stu-id="c2c17-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="c2c17-104">默认情况下，数据保护系统 [使用发现机制](xref:security/data-protection/configuration/default-settings) 来确定应在何处保存加密密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="c2c17-105">开发人员可以重写默认发现机制并手动指定位置。</span><span class="sxs-lookup"><span data-stu-id="c2c17-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="c2c17-106">如果指定显式密钥持久性位置，数据保护系统将注销静态密钥加密机制，这样密钥就不再静态加密。</span><span class="sxs-lookup"><span data-stu-id="c2c17-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="c2c17-107">建议你另外指定用于生产部署的 [显式密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest) 。</span><span class="sxs-lookup"><span data-stu-id="c2c17-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="c2c17-108">文件系统</span><span class="sxs-lookup"><span data-stu-id="c2c17-108">File system</span></span>

<span data-ttu-id="c2c17-109">若要配置基于文件系统的密钥存储库，请调用 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 配置例程，如下所示。</span><span class="sxs-lookup"><span data-stu-id="c2c17-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="c2c17-110">提供指向存储密钥的存储库的 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) ：</span><span class="sxs-lookup"><span data-stu-id="c2c17-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="c2c17-111">`DirectoryInfo`可以指向本地计算机上的目录，也可以指向网络共享上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c2c17-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="c2c17-112">如果指向本地计算机上的目录 (并且方案为仅本地计算机上的应用需要访问权限才能) 使用此存储库，请考虑在 Windows 上使用 [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) () 对静态密钥进行加密。</span><span class="sxs-lookup"><span data-stu-id="c2c17-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="c2c17-113">否则，请考虑使用 [x.509 证书](xref:security/data-protection/implementation/key-encryption-at-rest) 来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="c2c17-114">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="c2c17-114">Azure Storage</span></span>

<span data-ttu-id="c2c17-115">[AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package 允许将数据保护密钥存储在 Azure Blob 存储中。</span><span class="sxs-lookup"><span data-stu-id="c2c17-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="c2c17-116">可在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="c2c17-117">应用可 cookie 在多个服务器之间共享身份验证和 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="c2c17-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="c2c17-118">若要配置 Azure Blob 存储提供程序，请调用 [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) 重载之一。</span><span class="sxs-lookup"><span data-stu-id="c2c17-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="c2c17-119">如果 web 应用作为 Azure 服务运行，则可以使用 [microsoft.azure.services.appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)自动创建身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="c2c17-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

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

<span data-ttu-id="c2c17-120">查看 [有关配置服务到服务身份验证的更多详细信息。](/azure/key-vault/service-to-service-authentication)</span><span class="sxs-lookup"><span data-stu-id="c2c17-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="c2c17-121">Redis</span><span class="sxs-lookup"><span data-stu-id="c2c17-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c2c17-122">[AspNetCore. DataProtection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package 允许在 Redis 缓存中存储数据保护密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="c2c17-123">可在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="c2c17-124">应用可 cookie 在多个服务器之间共享身份验证和 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="c2c17-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c2c17-125">[AspNetCore. DataProtection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package 允许在 Redis 缓存中存储数据保护密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="c2c17-126">可在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="c2c17-127">应用可 cookie 在多个服务器之间共享身份验证和 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="c2c17-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c2c17-128">若要在 Redis 上进行配置，请调用 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) 重载之一：</span><span class="sxs-lookup"><span data-stu-id="c2c17-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="c2c17-129">若要在 Redis 上进行配置，请调用 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) 重载之一：</span><span class="sxs-lookup"><span data-stu-id="c2c17-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="c2c17-130">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="c2c17-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="c2c17-131">Stackexchange.redis. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="c2c17-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="c2c17-132">Azure Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="c2c17-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="c2c17-133">ASP.NET Core DataProtection 示例</span><span class="sxs-lookup"><span data-stu-id="c2c17-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="c2c17-134">注册表</span><span class="sxs-lookup"><span data-stu-id="c2c17-134">Registry</span></span>

<span data-ttu-id="c2c17-135">**仅适用于 Windows 部署。**</span><span class="sxs-lookup"><span data-stu-id="c2c17-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="c2c17-136">有时应用程序可能没有对文件系统的写访问权限。</span><span class="sxs-lookup"><span data-stu-id="c2c17-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="c2c17-137">请考虑一种方案，其中应用作为虚拟服务帐户运行 (如 *w3wp.exe* 的应用池标识) 。</span><span class="sxs-lookup"><span data-stu-id="c2c17-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe* 's app pool identity).</span></span> <span data-ttu-id="c2c17-138">在这些情况下，管理员可以设置可由服务帐户标识访问的注册表项。</span><span class="sxs-lookup"><span data-stu-id="c2c17-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="c2c17-139">调用 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) 扩展方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="c2c17-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="c2c17-140">提供一个 [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) ，指向应存储加密密钥的位置：</span><span class="sxs-lookup"><span data-stu-id="c2c17-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="c2c17-141">建议使用 [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) 来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="c2c17-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c2c17-142">Entity Framework Core</span></span>

<span data-ttu-id="c2c17-143">[AspNetCore. DataProtection. microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)包提供了一种机制，用于使用 Entity Framework Core 将数据保护密钥存储到数据库。</span><span class="sxs-lookup"><span data-stu-id="c2c17-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="c2c17-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore`NuGet 包必须添加到项目文件，而不是[AspNetCore 元包](xref:fundamentals/metapackage-app)的一部分。</span><span class="sxs-lookup"><span data-stu-id="c2c17-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c2c17-145">使用此包，可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="c2c17-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="c2c17-146">若要配置 EF Core 提供程序，请[调用 \<TContext> PersistKeysToDbContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)方法：</span><span class="sxs-lookup"><span data-stu-id="c2c17-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c2c17-147">泛型参数 `TContext` 必须从 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 继承，并实现 [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)：</span><span class="sxs-lookup"><span data-stu-id="c2c17-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="c2c17-148">创建 `DataProtectionKeys` 表。</span><span class="sxs-lookup"><span data-stu-id="c2c17-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c2c17-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c2c17-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c2c17-150">在 **包管理器控制台** 中执行以下命令 (PMC) 窗口：</span><span class="sxs-lookup"><span data-stu-id="c2c17-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c2c17-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c2c17-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c2c17-152">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c2c17-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="c2c17-153">`MyKeysContext` 是 `DbContext` 前面的代码示例中定义的。</span><span class="sxs-lookup"><span data-stu-id="c2c17-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="c2c17-154">如果你使用的是 `DbContext` 其他名称，请将你 `DbContext` 的名称替换为 `MyKeysContext` 。</span><span class="sxs-lookup"><span data-stu-id="c2c17-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="c2c17-155">`DataProtectionKeys`类/实体采用下表所示的结构。</span><span class="sxs-lookup"><span data-stu-id="c2c17-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="c2c17-156">属性/字段</span><span class="sxs-lookup"><span data-stu-id="c2c17-156">Property/Field</span></span> | <span data-ttu-id="c2c17-157">CLR 类型</span><span class="sxs-lookup"><span data-stu-id="c2c17-157">CLR Type</span></span> | <span data-ttu-id="c2c17-158">SQL 类型</span><span class="sxs-lookup"><span data-stu-id="c2c17-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="c2c17-159">`int`，PK， `IDENTITY(1,1)` ，not null</span><span class="sxs-lookup"><span data-stu-id="c2c17-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="c2c17-160">`nvarchar(MAX)`，null</span><span class="sxs-lookup"><span data-stu-id="c2c17-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="c2c17-161">`nvarchar(MAX)`，null</span><span class="sxs-lookup"><span data-stu-id="c2c17-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="c2c17-162">自定义密钥存储库</span><span class="sxs-lookup"><span data-stu-id="c2c17-162">Custom key repository</span></span>

<span data-ttu-id="c2c17-163">如果不适合使用机箱内机制，开发人员可以通过提供自定义 [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)来指定其自己的密钥持久性机制。</span><span class="sxs-lookup"><span data-stu-id="c2c17-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
