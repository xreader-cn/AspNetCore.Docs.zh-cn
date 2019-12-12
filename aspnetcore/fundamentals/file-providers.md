---
title: ASP.NET Core 中的文件提供程序
author: guardrex
description: 了解 ASP.NET Core 如何通过文件提供程序来抽象化文件系统访问。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: a454ca394546184968222ca2ca44d7159b19a12a
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944303"
---
# <a name="file-providers-in-aspnet-core"></a>ASP.NET Core 中的文件提供程序

作者：[Steve Smith](https://ardalis.com/) 和 [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 通过文件提供程序来抽象化文件系统访问。 在 ASP.NET Core 框架中使用文件提供程序：

* `IWebHostEnvironment` 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。
* [静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。
* [Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。
* .NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="file-provider-interfaces"></a>文件提供程序接口

主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。 `IFileProvider` 公开方法以实现以下目的：

* 获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。
* 获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。
* 设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。

`IFileInfo` 提供用于处理文件的方法和属性：

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期

可以使用 [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 方法从文件读取内容。

示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖关系注入](xref:fundamentals/dependency-injection)在应用中使用。

## <a name="file-provider-implementations"></a>文件提供程序实现

`IFileProvider` 有三种实现。

| 实现 | 说明 |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | 物理提供程序用于访问系统的物理文件。 |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | 清单嵌入式提供程序用于访问程序集中嵌入的文件。 |
| [CompositeFileProvider](#compositefileprovider) | 复合式提供程序提供对一个或多个其他提供程序中的文件和目录的合并访问。 |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。 `PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。 此范围界定可防止访问指定目录和子目录之外的文件系统。 创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。

直接实例化此提供程序时，必须提供目录路径并将其作为使用提供程序发出的所有请求的基路径。

下面的代码演示如何创建 `PhysicalFileProvider` 并用它来获取目录内容和文件信息：

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

前面的示例中的类型：

* `provider` 是 `IFileProvider`。
* `contents` 是 `IDirectoryContents`。
* `fileInfo` 是 `IFileInfo`。

文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。 该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。

示例应用使用 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) 在应用的 `Startup.ConfigureServices` 类中创建提供程序：

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。 `ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。

将包引用添加到 [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) 包的项目。

若要生成嵌入的文件清单，请将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。 指定要使用 [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。

示例应用创建 `ManifestEmbeddedFileProvider` 并将当前正在执行的程序集传递给其构造函数。

*Startup.cs*：

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

其他重载使你能够：

* 指定相对文件路径。
* 将文件范围限制到上次修改日期。
* 为包含嵌入文件清单的嵌入资源命名。

| 重载 | 说明 |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | 接受一个可选的 `root` 相对路径参数。 指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。 |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。 `lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。 |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。 `manifestName` 表示包含清单的嵌入资源的名称。 |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。 创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。

在示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件：

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>监视更改

[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 方法提供了一个方案来监视一个或多个文件或目录是否发生更改。 `Watch` 接受路径字符串，它可以使用 [glob 模式](#glob-patterns)指定多个文件。 `Watch` 返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。 更改令牌会公开以下内容：

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; 可检查此属性以确定是否已发生更改。
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 检测到指定的路径字符串发生更改时调用此属性。 每个更改令牌仅调用其关联的回调来响应单个更改。 若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。

在示例应用中，WatchConsole  控制台应用配置为每次修改了文本文件时显示一条消息：

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。 将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。

## <a name="glob-patterns"></a>glob 模式

文件系统路径使用名为 glob（或通配）模式  的通配符模式。 使用这些模式指定文件的组。 两个通配符分别是 `*` 和 `**`：

**`*`**  
匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。 匹配由文件路径中的 `/` 和 `.` 字符终止。

**`**`**  
匹配多个目录级别的任何内容。 可用于以递归方式匹配目录层次结构中的许多文件。

**glob 模式示例**

**`directory/file.txt`**  
匹配特定目录中的特定文件。

**`directory/*.txt`**  
匹配特定目录中带 .txt  扩展名的所有文件。

**`directory/*/appsettings.json`**  
匹配正好位于“目录”  文件夹中下一级目录中的所有 `appsettings.json` 文件。

**`directory/**/*.txt`**  
匹配在“目录”  文件夹下任何位置找到的带 .txt  扩展名的所有文件。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 通过文件提供程序来抽象化文件系统访问。 在 ASP.NET Core 框架中使用文件提供程序：

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。
* [静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。
* [Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。
* .NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="file-provider-interfaces"></a>文件提供程序接口

主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。 `IFileProvider` 公开方法以实现以下目的：

* 获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。
* 获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。
* 设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。

`IFileInfo` 提供用于处理文件的方法和属性：

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期

可以使用 [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 方法从文件读取内容。

示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖关系注入](xref:fundamentals/dependency-injection)在应用中使用。

## <a name="file-provider-implementations"></a>文件提供程序实现

`IFileProvider` 有三种实现。

| 实现 | 说明 |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | 物理提供程序用于访问系统的物理文件。 |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | 清单嵌入式提供程序用于访问程序集中嵌入的文件。 |
| [CompositeFileProvider](#compositefileprovider) | 复合式提供程序提供对一个或多个其他提供程序中的文件和目录的合并访问。 |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。 `PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。 此范围界定可防止访问指定目录和子目录之外的文件系统。 创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。

直接实例化此提供程序时，必须提供目录路径并将其作为使用提供程序发出的所有请求的基路径。

下面的代码演示如何创建 `PhysicalFileProvider` 并用它来获取目录内容和文件信息：

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

前面的示例中的类型：

* `provider` 是 `IFileProvider`。
* `contents` 是 `IDirectoryContents`。
* `fileInfo` 是 `IFileInfo`。

文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。 该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。

示例应用使用 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) 在应用的 `Startup.ConfigureServices` 类中创建提供程序：

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。 `ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。

若要生成嵌入的文件清单，请将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。 指定要使用 [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。

示例应用创建 `ManifestEmbeddedFileProvider` 并将当前正在执行的程序集传递给其构造函数。

*Startup.cs*：

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

其他重载使你能够：

* 指定相对文件路径。
* 将文件范围限制到上次修改日期。
* 为包含嵌入文件清单的嵌入资源命名。

| 重载 | 说明 |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | 接受一个可选的 `root` 相对路径参数。 指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。 |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。 `lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。 |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。 `manifestName` 表示包含清单的嵌入资源的名称。 |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。 创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。

在示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件：

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>监视更改

[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 方法提供了一个方案来监视一个或多个文件或目录是否发生更改。 `Watch` 接受路径字符串，它可以使用 [glob 模式](#glob-patterns)指定多个文件。 `Watch` 返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。 更改令牌会公开以下内容：

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; 可检查此属性以确定是否已发生更改。
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 检测到指定的路径字符串发生更改时调用此属性。 每个更改令牌仅调用其关联的回调来响应单个更改。 若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。

在示例应用中，WatchConsole  控制台应用配置为每次修改了文本文件时显示一条消息：

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。 将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。

## <a name="glob-patterns"></a>glob 模式

文件系统路径使用名为 glob（或通配）模式  的通配符模式。 使用这些模式指定文件的组。 两个通配符分别是 `*` 和 `**`：

**`*`**  
匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。 匹配由文件路径中的 `/` 和 `.` 字符终止。

**`**`**  
匹配多个目录级别的任何内容。 可用于以递归方式匹配目录层次结构中的许多文件。

**glob 模式示例**

**`directory/file.txt`**  
匹配特定目录中的特定文件。

**`directory/*.txt`**  
匹配特定目录中带 .txt  扩展名的所有文件。

**`directory/*/appsettings.json`**  
匹配正好位于“目录”  文件夹中下一级目录中的所有 `appsettings.json` 文件。

**`directory/**/*.txt`**  
匹配在“目录”  文件夹下任何位置找到的带 .txt  扩展名的所有文件。

::: moniker-end
