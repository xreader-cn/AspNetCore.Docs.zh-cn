---
title: 通过 dotnet-grpc 管理 Protobuf 参考
author: juntaoluo
description: 了解如何通过 dotnet grpc 全局工具添加、更新、删除和列出 Protobuf 引用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519032"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>通过 dotnet-grpc 管理 Protobuf 参考

作者：[John Luo](https://github.com/juntaoluo)

`dotnet-grpc` 是一个 .NET Core 全局工具，用于管理 .NET gRPC 项目中的[Protobuf （*proto*）](xref:grpc/basics#proto-file)引用。 该工具可用于添加、刷新、删除和列出 Protobuf 引用。

## <a name="installation"></a>安装

若要安装 `dotnet-grpc` [.Net Core 全局工具](/dotnet/core/tools/global-tools)，请运行以下命令：

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>添加引用

`dotnet-grpc` 可用于将 Protobuf 引用作为 @no__t 项添加到 *.csproj*文件：

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Protobuf 引用用于生成C#客户端和/或服务器资产。 @No__t-0 工具可以：

* 从磁盘上的本地文件创建 Protobuf 引用。
* 从 URL 指定的远程文件创建 Protobuf 引用。
* 确保将正确的 gRPC 包依赖项添加到项目。

例如，将 `Grpc.AspNetCore` 包添加到 web 应用。 `Grpc.AspNetCore` 包含 gRPC 服务器和客户端库和工具支持。 或者，将 `Grpc.Net.Client`、@no__t 1 和 `Google.Protobuf` 包（其中仅包含 gRPC 客户端库和工具支持）添加到控制台应用。

### <a name="add-file"></a>添加文件

@No__t-0 命令用于在磁盘上添加本地文件作为 Protobuf 引用。 提供的文件路径：

* 可以是相对于当前目录的路径，也可以是绝对路径。
* 可能包含用于基于模式[的文件组合的通配符](https://wikipedia.org/wiki/Glob_(programming))。

如果任何文件位于项目目录之外，则会添加一个 @no__t 0 元素，以在 Visual Studio 中的文件夹 `Protos` 下显示文件。

### <a name="usage"></a>用法

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>自变量

| 参数 | 描述 |
|-|-|
| 文件 | Protobuf 文件引用。 它们可以是本地 protobuf 文件的 glob 的路径。 |

#### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --项目 | 要操作的项目文件的路径。 如果未指定文件，此命令将在当前目录中搜索一个。
| -s | --服务 | 应生成的 gRPC 服务的类型。 如果指定 `Default`，则 `Both` 用于 Web 项目，`Client` 用于非 Web 项目。 接受的值为 `Both`，`Client`，`Default`，`None`，`Server`。
| -i | --附加-import-目录 | 解析 protobuf 文件的导入时要使用的其他目录。 这是以分号分隔的路径列表。
| | --访问 | 要用于生成C#的类的访问修饰符。 默认值为 `Public`。 接受的值为 `Internal`，@no__t 为-1。

### <a name="add-url"></a>添加 URL

@No__t-0 命令用于添加由源 URL 指定为 Protobuf 引用的远程文件。 必须提供文件路径才能指定下载远程文件的位置。 文件路径可以是相对于当前目录的路径，也可以是绝对路径。 如果文件路径在项目目录之外，则会添加一个 @no__t 0 元素，以在 Visual Studio 中的虚拟文件夹 `Protos` 下显示文件。

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
| -p | --项目 | 要操作的项目文件的路径。 如果未指定文件，此命令将在当前目录中搜索一个。
| -s | --服务 | 应生成的 gRPC 服务的类型。 如果指定 `Default`，则 `Both` 用于 Web 项目，`Client` 用于非 Web 项目。 接受的值为 `Both`，`Client`，`Default`，`None`，`Server`。
| -i | --附加-import-目录 | 解析 protobuf 文件的导入时要使用的其他目录。 这是以分号分隔的路径列表。
| | --访问 | 要用于生成C#的类的访问修饰符。 默认值是 `Public`。 接受的值为 `Internal`，@no__t 为-1。

## <a name="remove"></a>移除

@No__t-0 命令用于从 *.csproj*文件中删除 Protobuf 引用。 命令接受路径自变量和源 Url 作为参数。 工具：

* 仅删除 Protobuf 引用。
* 不会删除*proto*文件，即使它最初是从远程 URL 下载的也是如此。

### <a name="usage"></a>用法

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>自变量

| 参数 | 描述 |
|-|-|
| 引用 | 要移除的 protobuf 引用的 Url 或文件路径。 |

### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --项目 | 要操作的项目文件的路径。 如果未指定文件，此命令将在当前目录中搜索一个。

## <a name="refresh"></a>“刷新”

@No__t-0 命令用于使用源 URL 中的最新内容更新远程引用。 下载文件路径和源 URL 都可以用来指定要更新的引用。 注意:

* 比较文件内容的哈希值，以确定是否应更新本地文件。
* 不比较时间戳信息。

如果需要更新，此工具始终会将本地文件替换为远程文件。

### <a name="usage"></a>用法

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>自变量

| 参数 | 描述 |
|-|-|
| 引用 | 应更新的远程 protobuf 引用的 Url 或文件路径。 将此参数保留为空，以刷新所有远程引用。 |

### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --项目 | 要操作的项目文件的路径。 如果未指定文件，此命令将在当前目录中搜索一个。
| | --模拟运行 | 输出将更新但不下载任何新内容的文件的列表。

## <a name="list"></a>列表

@No__t-0 命令用于显示项目文件中的所有 Protobuf 引用。 如果列的所有值均为默认值，则可以省略该列。

### <a name="usage"></a>用法

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>选项

| 短选项 | 长选项 | 描述 |
|-|-|-|
| -p | --项目 | 要操作的项目文件的路径。 如果未指定文件，此命令将在当前目录中搜索一个。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
