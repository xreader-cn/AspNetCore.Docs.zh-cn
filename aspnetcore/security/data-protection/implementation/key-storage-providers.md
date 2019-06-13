---
title: 在 ASP.NET Core 中的密钥存储提供程序
author: rick-anderson
description: 了解有关 ASP.NET Core 以及如何配置密钥的存储位置中的密钥存储提供程序。
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 64c7e6b25d5b4acc72e96747a77826efaeb693fd
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034767"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="b570d-103">在 ASP.NET Core 中的密钥存储提供程序</span><span class="sxs-lookup"><span data-stu-id="b570d-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="b570d-104">数据保护系统[默认情况下使用的发现机制](xref:security/data-protection/configuration/default-settings)来确定应该保留加密密钥的位置。</span><span class="sxs-lookup"><span data-stu-id="b570d-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="b570d-105">开发人员可以重写默认的发现机制，并手动指定的位置。</span><span class="sxs-lookup"><span data-stu-id="b570d-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="b570d-106">如果指定了显式密钥持久性位置，数据保护系统注销 rest 机制，在默认密钥加密，因此不再静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="b570d-107">我们建议你此外[指定一个显式的密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)对于生产部署。</span><span class="sxs-lookup"><span data-stu-id="b570d-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="b570d-108">文件系统</span><span class="sxs-lookup"><span data-stu-id="b570d-108">File system</span></span>

<span data-ttu-id="b570d-109">若要配置的文件基于系统的密钥存储库，请调用[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)配置例程，如下所示。</span><span class="sxs-lookup"><span data-stu-id="b570d-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="b570d-110">提供[DirectoryInfo](/dotnet/api/system.io.directoryinfo)指向存储密钥的存储库：</span><span class="sxs-lookup"><span data-stu-id="b570d-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="b570d-111">`DirectoryInfo`可以指向本地计算机上的某个目录，也可以指向网络共享上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b570d-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="b570d-112">如果指向本地计算机上的目录 （和的方案是只在本地计算机上的应用需要使用此存储库的访问权限），请考虑使用[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (在 Windows) 来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="b570d-113">否则，请考虑使用[X.509 证书](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="b570d-114">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="b570d-114">Azure Storage</span></span>

<span data-ttu-id="b570d-115">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)包允许在 Azure Blob 存储中存储数据保护密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="b570d-116">可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="b570d-117">应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="b570d-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="b570d-118">若要配置 Azure Blob 存储提供程序，请调用之一[PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)重载。</span><span class="sxs-lookup"><span data-stu-id="b570d-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span> 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="b570d-119">如果 web 应用正在作为一项 Azure 服务，身份验证令牌可以自动使用创建[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)。</span><span class="sxs-lookup"><span data-stu-id="b570d-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [ Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span> 

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

<span data-ttu-id="b570d-120">请参阅[有关配置服务到服务身份验证的更多详细信息。](/azure/key-vault/service-to-service-authentication)</span><span class="sxs-lookup"><span data-stu-id="b570d-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="b570d-121">Redis</span><span class="sxs-lookup"><span data-stu-id="b570d-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b570d-122">[Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/)包允许将数据保护密钥存储在 Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="b570d-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="b570d-123">可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="b570d-124">应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="b570d-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b570d-125">[Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/)包允许将数据保护密钥存储在 Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="b570d-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="b570d-126">可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="b570d-127">应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="b570d-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b570d-128">若要配置 Redis，调用之一[PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis)重载：</span><span class="sxs-lookup"><span data-stu-id="b570d-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="b570d-129">若要配置 Redis，调用之一[PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis)重载：</span><span class="sxs-lookup"><span data-stu-id="b570d-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="b570d-130">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="b570d-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="b570d-131">StackExchange.Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="b570d-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="b570d-132">Azure Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="b570d-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="b570d-133">aspnet/DataProtection 示例</span><span class="sxs-lookup"><span data-stu-id="b570d-133">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="b570d-134">注册表</span><span class="sxs-lookup"><span data-stu-id="b570d-134">Registry</span></span>

<span data-ttu-id="b570d-135">**仅适用于 Windows 的部署。**</span><span class="sxs-lookup"><span data-stu-id="b570d-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="b570d-136">有时应用程序可能没有到文件系统的写访问权限。</span><span class="sxs-lookup"><span data-stu-id="b570d-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="b570d-137">请考虑应用正在作为虚拟服务帐户的方案 (如*w3wp.exe*的应用程序池标识)。</span><span class="sxs-lookup"><span data-stu-id="b570d-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="b570d-138">在这些情况下，管理员可以预配的服务帐户标识都可访问的注册表项。</span><span class="sxs-lookup"><span data-stu-id="b570d-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="b570d-139">调用[PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry)扩展方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="b570d-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="b570d-140">提供[RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey)指向存储加密密钥的位置：</span><span class="sxs-lookup"><span data-stu-id="b570d-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="b570d-141">我们建议使用[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="b570d-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b570d-142">Entity Framework Core</span></span>

<span data-ttu-id="b570d-143">[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)包提供了用于存储到使用 Entity Framework Core 的数据库的数据保护密钥的机制。</span><span class="sxs-lookup"><span data-stu-id="b570d-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="b570d-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet 包必须将添加到项目文件中，它不属于[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="b570d-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b570d-145">通过此包，可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="b570d-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="b570d-146">若要配置 EF Core 提供程序，请调用[ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)方法：</span><span class="sxs-lookup"><span data-stu-id="b570d-146">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

<span data-ttu-id="b570d-147">泛型参数`TContext`，必须继承自[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)并[IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="b570d-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="b570d-148">创建`DataProtectionKeys`表。</span><span class="sxs-lookup"><span data-stu-id="b570d-148">Create the `DataProtectionKeys` table.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b570d-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b570d-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b570d-150">执行以下命令中的**程序包管理器控制台**(PMC) 窗口：</span><span class="sxs-lookup"><span data-stu-id="b570d-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b570d-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b570d-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b570d-152">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b570d-152">Execute the following commands in a command shell:</span></span>

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="b570d-153">`MyKeysContext` 是`DbContext`在上面的代码示例中定义。</span><span class="sxs-lookup"><span data-stu-id="b570d-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="b570d-154">如果您使用的`DbContext`使用不同的名称，替换为你`DbContext`名称`MyKeysContext`。</span><span class="sxs-lookup"><span data-stu-id="b570d-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="b570d-155">`DataProtectionKeys`类/实体采用下表中所示的结构。</span><span class="sxs-lookup"><span data-stu-id="b570d-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="b570d-156">属性/字段</span><span class="sxs-lookup"><span data-stu-id="b570d-156">Property/Field</span></span> | <span data-ttu-id="b570d-157">CLR 类型</span><span class="sxs-lookup"><span data-stu-id="b570d-157">CLR Type</span></span> | <span data-ttu-id="b570d-158">SQL 类型</span><span class="sxs-lookup"><span data-stu-id="b570d-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="b570d-159">`int`PK，不为 null</span><span class="sxs-lookup"><span data-stu-id="b570d-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="b570d-160">`nvarchar(MAX)`null</span><span class="sxs-lookup"><span data-stu-id="b570d-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="b570d-161">`nvarchar(MAX)`null</span><span class="sxs-lookup"><span data-stu-id="b570d-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="b570d-162">自定义密钥存储库</span><span class="sxs-lookup"><span data-stu-id="b570d-162">Custom key repository</span></span>

<span data-ttu-id="b570d-163">如果不适当的内置机制，开发人员可以通过提供自定义指定自己的密钥持久性机制[IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)。</span><span class="sxs-lookup"><span data-stu-id="b570d-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
