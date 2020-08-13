---
title: 将 LibMan CLI 与 ASP.NET Core 结合使用
author: scottaddie
description: 了解如何在 ASP.NET Core 项目中使用 LibMan CLI。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: 6e1ab9c540e1714f2f8cd6e6f2603e4d589a7d2b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013471"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>将 LibMan CLI 与 ASP.NET Core 结合使用

作者：[Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI 是一种跨平台工具，在所有支持的 .NET Core 的位置都可得到支持。

## <a name="prerequisites"></a>先决条件

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>安装

安装 LibMan CLI：

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

从 [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 包安装 [.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)。

从特定 NuGet 包源安装 LibMan CLI：

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

在前面的示例中，从本地 Windows 计算机的 C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg 文件安装 .NET Core 全局工具。

## <a name="usage"></a>用法

成功安装 CLI 之后，可以使用以下命令：

```console
libman
```

查看已安装的 CLI 版本：

```console
libman --version
```

查看可用 CLI 命令：

```console
libman --help
```

前面的命令显示类似于以下内容的输出：

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

以下部分概述了可用的 CLI 命令。

## <a name="initialize-libman-in-the-project"></a>在项目中初始化 LibMan

`libman init` 命令会创建一个 libman.json 文件（如果不存在）。 该文件会使用默认项模板内容进行创建。

### <a name="synopsis"></a>摘要

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>选项

`libman init` 命令可以使用以下选项：

* `-d|--default-destination <PATH>`

  相对于当前文件夹的路径。 如果未在 libman.json 中为库定义 `destination` 属性，则库文件会安装在此位置。 `<PATH>` 值会写入 libman.json 的 `defaultDestination` 属性。

* `-p|--default-provider <PROVIDER>`

  未为给定库定义提供程序时要使用的提供程序。 `<PROVIDER>` 值会写入 libman.json 的 `defaultProvider` 属性。 将 `<PROVIDER>` 替换为以下值之一：

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

在 ASP.NET Core 项目中创建 libman.json 文件：

* 导航到项目根。
* 运行下面的命令：

  ```console
  libman init
  ```

* 键入默认提供程序的名称，或按 `Enter` 以使用默认 CDNJS 提供程序。 有效值包括：

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman 初始化命令 - 默认提供程序](_static/libman-init-provider.png)

具有以下内容的 libman.json 文件会添加到项目根：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>添加库文件

`libman install` 命令会将库文件下载并安装到项目中。 如果 libman.json 文件不存在，则会添加一个。 libman.json 文件会进行修改，以存储库文件的配置详细信息。

### <a name="synopsis"></a>摘要

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>自变量

`LIBRARY`

要安装的库的名称。 此名称可以包含版本号表示法（例如 `@1.2.0`）。

### <a name="options"></a>选项

`libman install` 命令可以使用以下选项：

* `-d|--destination <PATH>`

  用于安装库的位置。 如果未指定，则使用默认位置。 如果 libman.json 中未指定 `defaultDestination` 属性，则此选项是必需的。

* `--files <FILE>`

  指定要从库安装的文件的名称。 如果未指定，则会安装库中的所有文件。 为每个要安装的文件提供一个 `--files` 选项。 也支持相对路径。 例如：`--files dist/browser/signalr.js`。

* `-p|--provider <PROVIDER>`

  用于库获取的提供程序的名称。 将 `<PROVIDER>` 替换为以下值之一：
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  如果未指定，则使用 libman.json 中的 `defaultProvider` 属性。 如果 libman.json 中未指定 `defaultProvider` 属性，则此选项是必需的。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

请考虑以下 libman.json 文件：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

使用 CDNJS 提供程序将 jQuery 版本 3.2.1 jquery.min.js 文件安装到 wwwroot/scripts/jquery 文件夹：

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

libman.json 文件类似于以下内容：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

使用文件系统提供程序从 C:\\temp\\contosoCalendar\\ 安装 calendar.js 和 calendar.css：

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

由于两个原因而出现以下提示：

* libman.json 文件不包含 `defaultDestination` 属性。
* `libman install` 命令不包含 `-d|--destination` 选项。

![libman 安装命令 - 目标](_static/libman-install-destination.png)

接受默认目标之后，libman.json 文件类似于以下内容：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>还原库文件

`libman restore` 命令安装在 libman.json 中定义的库文件。 适用以下规则：

* 如果项目根中不存在 libman.json 文件，则会返回错误。
* 如果库指定了提供程序，则会忽略 libman.json 中的 `defaultProvider` 属性。
* 如果库指定了目标，则会忽略 libman.json 中的 `defaultDestination` 属性。

### <a name="synopsis"></a>摘要

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>选项

`libman restore` 命令可以使用以下选项：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

还原在 libman.json 中定义的库文件：

```console
libman restore
```

## <a name="delete-library-files"></a>删除库文件

`libman clean` 命令会删除之前通过 LibMan 还原的库文件。 在此操作之后成为空的文件夹会删除。 libman.json 的 `libraries` 属性中库文件的关联配置不会删除。

### <a name="synopsis"></a>摘要

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>选项

`libman clean` 命令可以使用以下选项：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

删除通过 LibMan 安装的库文件：

```console
libman clean
```

## <a name="uninstall-library-files"></a>卸载库文件

`libman uninstall` 命令：

* 从 libman.json 中的目标删除与指定库关联的所有文件。
* 从 libman.json 中删除关联库配置。

在下述情况中会发生错误：

* 项目根中不存在 libman.json 文件。
* 指定库不存在。

如果安装了多个具有相同名称的库，则系统会提示选择一个。

### <a name="synopsis"></a>摘要

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>自变量

`LIBRARY`

要卸载的库的名称。 此名称可以包含版本号表示法（例如 `@1.2.0`）。

### <a name="options"></a>选项

`libman uninstall` 命令可以使用以下选项：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

请考虑以下 libman.json 文件：

[!code-json[](samples/LibManSample/libman.json)]

* 若要卸载 jQuery，以下任一命令都会成功：

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* 卸载通过 `filesystem` 提供程序安装的 Lodash 文件：

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>更新库版本

`libman update` 命令会将通过 LibMan 安装的库更新为指定版本。

在下述情况中会发生错误：

* 项目根中不存在 libman.json 文件。
* 指定库不存在。

如果安装了多个具有相同名称的库，则系统会提示选择一个。

### <a name="synopsis"></a>摘要

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>自变量

`LIBRARY`

要更新的库的名称。

### <a name="options"></a>选项

`libman update` 命令可以使用以下选项：

* `-pre`

  获取库的最新预发行版本。

* `--to <VERSION>`

  获取库的特定版本。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

* 将 jQuery 更新为最新版本：

  ```console
  libman update jquery
  ```

* 将 jQuery 更新为版本 3.3.1：

  ```console
  libman update jquery --to 3.3.1
  ```

* 将 jQuery 更新为最新预发行版本：

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>管理库缓存

`libman cache` 命令会管理 LibMan 库缓存。 `filesystem` 提供程序不使用库缓存。

### <a name="synopsis"></a>摘要

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>自变量

`PROVIDER`

仅与 `clean` 命令一起使用。 指定要清除的提供程序缓存。 有效值包括：

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>选项

`libman cache` 命令可以使用以下选项：

* `--files`

  列出缓存的文件的名称。

* `--libraries`

  列出缓存的库的名称。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

* 若要查看每个提供程序的缓存库的名称，请使用以下命令之一：

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  显示了类似下面的输出：

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* 查看每个提供程序的缓存库文件的名称：

  ```console
  libman cache list --files
  ```

  显示了类似下面的输出：

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  请注意，前面的输出显示，jQuery 版本3.2.1 和3.3.1 缓存在 CDNJS 提供程序下。

* 清空 CDNJS 提供程序的库缓存：

  ```console
  libman cache clean cdnjs
  ```

  清空 CDNJS 提供程序缓存之后，`libman cache list` 命令会显示以下内容：

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* 为所有支持的提供程序清空缓存：

  ```console
  libman cache clean
  ```

  清空所有提供程序缓存之后，`libman cache list` 命令会显示以下内容：

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>其他资源

* [安装全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan GitHub 存储库](https://github.com/aspnet/LibraryManager)
