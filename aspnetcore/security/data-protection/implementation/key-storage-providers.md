---
title: 在 ASP.NET Core 中的密钥存储提供程序
author: rick-anderson
description: 了解有关 ASP.NET Core 以及如何配置密钥的存储位置中的密钥存储提供程序。
ms.author: riande
ms.date: 12/19/2018
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: d6dabc9e4581e0891d1dd14f73e086d50b45bba4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897444"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="6fb13-103">在 ASP.NET Core 中的密钥存储提供程序</span><span class="sxs-lookup"><span data-stu-id="6fb13-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="6fb13-104">数据保护系统[默认情况下使用的发现机制](xref:security/data-protection/configuration/default-settings)来确定应该保留加密密钥的位置。</span><span class="sxs-lookup"><span data-stu-id="6fb13-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="6fb13-105">开发人员可以重写默认的发现机制，并手动指定的位置。</span><span class="sxs-lookup"><span data-stu-id="6fb13-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="6fb13-106">如果指定了显式密钥持久性位置，数据保护系统注销 rest 机制，在默认密钥加密，因此不再静态加密密钥。</span><span class="sxs-lookup"><span data-stu-id="6fb13-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="6fb13-107">我们建议你此外[指定一个显式的密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)对于生产部署。</span><span class="sxs-lookup"><span data-stu-id="6fb13-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="6fb13-108">文件系统</span><span class="sxs-lookup"><span data-stu-id="6fb13-108">File system</span></span>

<span data-ttu-id="6fb13-109">若要配置的文件基于系统的密钥存储库，请调用[PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)配置例程，如下所示。</span><span class="sxs-lookup"><span data-stu-id="6fb13-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="6fb13-110">提供[DirectoryInfo](/dotnet/api/system.io.directoryinfo)指向存储密钥的存储库：</span><span class="sxs-lookup"><span data-stu-id="6fb13-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="6fb13-111">`DirectoryInfo`可以指向本地计算机上的某个目录，也可以指向网络共享上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6fb13-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="6fb13-112">如果指向本地计算机上的目录 （和的方案是只在本地计算机上的应用需要使用此存储库的访问权限），请考虑使用[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (在 Windows) 来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="6fb13-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="6fb13-113">否则，请考虑使用[X.509 证书](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="6fb13-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-and-redis"></a><span data-ttu-id="6fb13-114">Azure 和 Redis</span><span class="sxs-lookup"><span data-stu-id="6fb13-114">Azure and Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6fb13-115">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)并[Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/)包允许将数据保护密钥存储在 Azure 存储或 Redis缓存。</span><span class="sxs-lookup"><span data-stu-id="6fb13-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="6fb13-116">可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="6fb13-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="6fb13-117">应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="6fb13-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6fb13-118">[Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)并[Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/)包允许将数据保护密钥存储在 Azure 存储或 Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="6fb13-118">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) and [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) packages allow storing data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="6fb13-119">可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="6fb13-119">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="6fb13-120">应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="6fb13-120">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

<span data-ttu-id="6fb13-121">若要配置 Azure Blob 存储提供程序，请调用之一[PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)重载：</span><span class="sxs-lookup"><span data-stu-id="6fb13-121">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6fb13-122">若要配置 Redis，调用之一[PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis)重载：</span><span class="sxs-lookup"><span data-stu-id="6fb13-122">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="6fb13-123">若要配置 Redis，调用之一[PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis)重载：</span><span class="sxs-lookup"><span data-stu-id="6fb13-123">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="6fb13-124">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="6fb13-124">For more information, see the following topics:</span></span>

* [<span data-ttu-id="6fb13-125">StackExchange.Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="6fb13-125">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="6fb13-126">Azure Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="6fb13-126">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="6fb13-127">aspnet/DataProtection 示例</span><span class="sxs-lookup"><span data-stu-id="6fb13-127">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="6fb13-128">注册表</span><span class="sxs-lookup"><span data-stu-id="6fb13-128">Registry</span></span>

<span data-ttu-id="6fb13-129">**仅适用于 Windows 的部署。**</span><span class="sxs-lookup"><span data-stu-id="6fb13-129">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="6fb13-130">有时应用程序可能没有到文件系统的写访问权限。</span><span class="sxs-lookup"><span data-stu-id="6fb13-130">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="6fb13-131">请考虑应用正在作为虚拟服务帐户的方案 (如*w3wp.exe*的应用程序池标识)。</span><span class="sxs-lookup"><span data-stu-id="6fb13-131">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="6fb13-132">在这些情况下，管理员可以预配的服务帐户标识都可访问的注册表项。</span><span class="sxs-lookup"><span data-stu-id="6fb13-132">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="6fb13-133">调用[PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry)扩展方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="6fb13-133">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="6fb13-134">提供[RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey)指向存储加密密钥的位置：</span><span class="sxs-lookup"><span data-stu-id="6fb13-134">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="6fb13-135">我们建议使用[Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest)来加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="6fb13-135">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="6fb13-136">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6fb13-136">Entity Framework Core</span></span>

<span data-ttu-id="6fb13-137">[Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/)包提供了用于存储到使用 Entity Framework Core 的数据库的数据保护密钥的机制。</span><span class="sxs-lookup"><span data-stu-id="6fb13-137">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="6fb13-138">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet 包必须将添加到项目文件中，它不属于[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="6fb13-138">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6fb13-139">通过此包，可以在 web 应用的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="6fb13-139">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="6fb13-140">若要配置 EF Core 提供程序，请调用[ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext)方法：</span><span class="sxs-lookup"><span data-stu-id="6fb13-140">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

<span data-ttu-id="6fb13-141">泛型参数`TContext`，必须继承自[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)并[IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="6fb13-141">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="6fb13-142">创建`DataProtectionKeys`表。</span><span class="sxs-lookup"><span data-stu-id="6fb13-142">Create the `DataProtectionKeys` table.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6fb13-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6fb13-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6fb13-144">执行以下命令中的**程序包管理器控制台**(PMC) 窗口：</span><span class="sxs-lookup"><span data-stu-id="6fb13-144">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6fb13-145">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6fb13-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6fb13-146">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6fb13-146">Execute the following commands in a command shell:</span></span>

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="6fb13-147">`MyKeysContext` 是`DbContext`在上面的代码示例中定义。</span><span class="sxs-lookup"><span data-stu-id="6fb13-147">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="6fb13-148">如果您使用的`DbContext`使用不同的名称，替换为你`DbContext`名称`MyKeysContext`。</span><span class="sxs-lookup"><span data-stu-id="6fb13-148">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="6fb13-149">`DataProtectionKeys`类/实体采用下表中所示的结构。</span><span class="sxs-lookup"><span data-stu-id="6fb13-149">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="6fb13-150">属性/字段</span><span class="sxs-lookup"><span data-stu-id="6fb13-150">Property/Field</span></span> | <span data-ttu-id="6fb13-151">CLR 类型</span><span class="sxs-lookup"><span data-stu-id="6fb13-151">CLR Type</span></span> | <span data-ttu-id="6fb13-152">SQL 类型</span><span class="sxs-lookup"><span data-stu-id="6fb13-152">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="6fb13-153">`int`PK，不为 null</span><span class="sxs-lookup"><span data-stu-id="6fb13-153">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="6fb13-154">`nvarchar(MAX)`null</span><span class="sxs-lookup"><span data-stu-id="6fb13-154">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="6fb13-155">`nvarchar(MAX)`null</span><span class="sxs-lookup"><span data-stu-id="6fb13-155">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="6fb13-156">自定义密钥存储库</span><span class="sxs-lookup"><span data-stu-id="6fb13-156">Custom key repository</span></span>

<span data-ttu-id="6fb13-157">如果不适当的内置机制，开发人员可以通过提供自定义指定自己的密钥持久性机制[IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)。</span><span class="sxs-lookup"><span data-stu-id="6fb13-157">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
