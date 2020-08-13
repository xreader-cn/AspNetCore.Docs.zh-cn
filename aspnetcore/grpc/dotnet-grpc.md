---
title: 通过 dotnet-grpc 管理 Protobuf 参考
author: juntaoluo
description: 了解如何通过 dotnet-grpc 全局工具添加、更新、删除和列出 Protobuf 引用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: d41958d586f54d5944af187933f2b0248f763171
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016124"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>通过 dotnet-grpc 管理 Protobuf 参考

作者：[John Luo](https://github.com/juntaoluo)

`dotnet-grpc` 是一种 .NET Core 全局工具，用于在 .NET gRPC 项目中管理 [Protobuf (.proto)](xref:grpc/basics#proto-file) 引用。 该工具可以用于添加、刷新、删除和列出 Protobuf 引用。

## <a name="installation"></a>安装

若要安装 `dotnet-grpc` [.NET Core 全局工具](/dotnet/core/tools/global-tools)，请运行以下命令：

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>添加引用

`dotnet-grpc` 可以用于将 Protobuf 引用作为 `<Protobuf />` 项添加到 .csproj 文件：

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Protobuf 引用用于生成 C# 客户端和/或服务器资产。 `dotnet-grpc` 工具可以：

* 从磁盘上的本地文件创建 Protobuf 引用。
* 从 URL 指定的远程文件创建 Protobuf 引用。
* 确保将正确的 gRPC 包依赖项添加到项目。

例如，将 `Grpc.AspNetCore` 包添加到 Web 应用。 `Grpc.AspNetCore` 包含 gRPC 服务器和客户端库以及工具支持。 或者，将 `Grpc.Net.Client`、`Grpc.Tools` 和 `Google.Protobuf` 包（其中仅包含 gRPC 客户端库和工具支持）添加到控制台应用。

### <a name="add-file"></a>添加文件

`add-file` 命令用于将磁盘上的本地文件添加为 Protobuf 引用。 提供的文件路径：

* 可以是当前目录的相对路径，也可以是绝对路径。
* 可以包含用于基于模式的文件[通配](https://wikipedia.org/wiki/Glob_(programming))的通配符。

如果任何文件处于项目目录之外，则会添加一个 `Link` 元素，以在 Visual Studio 中的文件夹 `Protos` 下显示该文件。

### <a name="usage"></a>用法

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>自变量

| 参数 | 描述 |
|-|-|
| 文件 | Protobuf 文件引用。 这些可以是本地 protobuf 文件的 glob 的路径。 |

#### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --project | 要操作的项目文件的路径。 如果未指定文件，则该命令会在当前目录中搜索一个文件。
| -S | --services | 应生成的 gRPC 服务的类型。 如果指定 `Default`，则 `Both` 用于 Web 项目，而 `Client` 用于非 Web 项目。 接受的值包括 `Both`、`Client`、`Default`、`None` 和 `Server`。
| -o | --additional-import-dirs | 解析 protobuf 文件的导入时要使用的其他目录。 这是以分号分隔的路径列表。
| | --access | 要用于生成的 C# 类的访问修饰符。 默认值为 `Public`。 接受的值为 `Internal` 和 `Public`。

### <a name="add-url"></a>添加 URL

`add-url` 命令用于将源 URL 指定的远程文件添加为 Protobuf 引用。 必须提供文件路径才能指定下载远程文件的位置。 文件路径可以是当前目录的相对路径，也可以是绝对路径。 如果文件路径处于项目目录之外，则会添加一个 `Link` 元素，以在 Visual Studio 中的虚拟文件夹 `Protos` 下显示该文件。

### <a name="usage"></a>用法

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>自变量

| 参数 | 描述 |
|-|-|
| URL | 远程 protobuf 文件的 URL。 |

#### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -o | --output | 指定远程 protobuf 文件的下载路径。 这是必需选项。
| -p | --project | 要操作的项目文件的路径。 如果未指定文件，则该命令会在当前目录中搜索一个文件。
| -S | --services | 应生成的 gRPC 服务的类型。 如果指定 `Default`，则 `Both` 用于 Web 项目，而 `Client` 用于非 Web 项目。 接受的值包括 `Both`、`Client`、`Default`、`None` 和 `Server`。
| -o | --additional-import-dirs | 解析 protobuf 文件的导入时要使用的其他目录。 这是以分号分隔的路径列表。
| | --access | 要用于生成的 C# 类的访问修饰符。 默认值是 `Public`。 接受的值为 `Internal` 和 `Public`。

## <a name="remove"></a>删除

`remove` 命令用于从 .csproj 文件中删除 Protobuf 引用。 该命令接受路径参数和源 URL 作为参数。 工具：

* 仅删除 Protobuf 引用。
* 不会删除 .proto 文件，即使它最初是从远程 URL 下载也是如此。

### <a name="usage"></a>用法

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>自变量

| 参数 | 描述 |
|-|-|
| 引用 | 要删除的 protobuf 引用的 URL 或文件路径。 |

### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --project | 要操作的项目文件的路径。 如果未指定文件，则该命令会在当前目录中搜索一个文件。

## <a name="refresh"></a>刷新

`refresh` 命令用于使用来自源 URL 的最新内容更新远程引用。 下载文件路径和源 URL 都可以用于指定要更新的引用。 注意：

* 会比较文件内容的哈希，以确定是否应更新本地文件。
* 不会比较时间戳信息。

如果需要更新，则该工具始终将本地文件替换为远程文件。

### <a name="usage"></a>用法

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>自变量

| 参数 | 描述 |
|-|-|
| 引用 | 应更新的远程 protobuf 引用的 URL 或文件路径。 将此参数保留为空，以刷新所有远程引用。 |

### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --project | 要操作的项目文件的路径。 如果未指定文件，则该命令会在当前目录中搜索一个文件。
| | --dry-run | 输出将更新的文件的列表，而不下载任何新内容。

## <a name="list"></a>列表

`list` 命令用于显示项目文件中的所有 Protobuf 引用。 如果某列的所有值都是默认值，则可以省略该列。

### <a name="usage"></a>用法

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --project | 要操作的项目文件的路径。 如果未指定文件，则该命令会在当前目录中搜索一个文件。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
