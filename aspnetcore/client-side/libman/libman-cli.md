---
title: 将 LibMan CLI 与 ASP.NET Core
author: scottaddie
description: 了解如何在 ASP.NET Core 项目中使用 LibMan CLI。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928361"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>将 LibMan CLI 与 ASP.NET Core

作者：[Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI 是一个跨平台工具，在支持 .net Core 的任何地方都受支持。

## <a name="prerequisites"></a>先决条件

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>安装

安装 LibMan CLI：

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[.Net Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)是从[LibraryManager](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 包安装的。

若要从特定的 NuGet 包源安装 LibMan CLI，请执行以下操作：

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

在前面的示例中，从本地 Windows 计算机的*C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg*文件安装 .Net Core 全局工具。

## <a name="usage"></a>用量

成功安装 CLI 后，可以使用以下命令：

```console
libman
```

查看已安装的 CLI 版本：

```console
libman --version
```

查看可用的 CLI 命令：

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

`libman init` 命令将创建一个*libman*文件（如果不存在）。 将用默认项模板内容创建文件。

### <a name="synopsis"></a>摘要

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>选项

`libman init` 命令可以使用以下选项：

* `-d|--default-destination <PATH>`

  相对于当前文件夹的路径。 如果未在*libman*中为库定义 `destination` 属性，则会在此位置安装库文件。 `<PATH>` 值将写入*libman*的 `defaultDestination` 属性。

* `-p|--default-provider <PROVIDER>`

  为给定库定义了提供程序时要使用的提供程序。 `<PROVIDER>` 值将写入*libman*的 `defaultProvider` 属性。 将 `<PROVIDER>` 替换为以下值之一：

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

在 ASP.NET Core 项目中创建*libman*文件：

* 导航到项目根。
* 运行下面的命令：

  ```console
  libman init
  ```

* 键入默认提供程序的名称，或按 `Enter` 使用默认的 CDNJS 提供程序。 有效值包括：

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 命令-默认提供程序](_static/libman-init-provider.png)

将*libman*文件添加到项目根，其中包含以下内容：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>添加库文件

`libman install` 命令将库文件下载并安装到项目中。 如果不存在*libman*文件，则添加一个。 修改*libman*文件以存储库文件的配置详细信息。

### <a name="synopsis"></a>摘要

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>参数

`LIBRARY`

要安装的库的名称。 此名称可以包含版本号表示法（例如 `@1.2.0`）。

### <a name="options"></a>选项

`libman install` 命令可以使用以下选项：

* `-d|--destination <PATH>`

  库的安装位置。 如果未指定，则使用默认位置。 如果*libman*中未指定 `defaultDestination` 属性，则此选项是必需的。

* `--files <FILE>`

  指定要从库中安装的文件的名称。 如果未指定，则会安装库中的所有文件。 为每个要安装的文件提供一个 `--files` 选项。 还支持相对路径。 例如：`--files dist/browser/signalr.js`。

* `-p|--provider <PROVIDER>`

  用于库获取的提供程序的名称。 将 `<PROVIDER>` 替换为以下值之一：
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  如果未指定，则使用*libman*中的 `defaultProvider` 属性。 如果*libman*中未指定 `defaultProvider` 属性，则此选项是必需的。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

请考虑以下*libman*文件：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

使用 CDNJS 提供程序将 jQuery 版本 3.2.1 *jquery*文件安装到*wwwroot/scripts/jQuery*文件夹：

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

*Libman*文件如下所示：

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

使用文件系统提供程序从*C：\\temp\\contosoCalendar\\* 中安装*node.js*和*calendar*文件：

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

出现以下两个原因的提示：

* *Libman*文件不包含 `defaultDestination` 属性。
* `libman install` 命令不包含 `-d|--destination` 选项。

![libman 安装命令-目标](_static/libman-install-destination.png)

接受默认目标后， *libman*文件如下所示：

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

## <a name="restore-library-files"></a>库将文件还原

`libman restore` 命令安装在*libman*中定义的库文件。 适用以下规则：

* 如果项目根目录中不存在*libman*文件，则会返回错误。
* 如果库指定提供程序，则会忽略*libman*中的 `defaultProvider` 属性。
* 如果库指定一个目标，则将忽略*libman*中的 `defaultDestination` 属性。

### <a name="synopsis"></a>摘要

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>选项

`libman restore` 命令可以使用以下选项：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

若要还原*libman*中定义的库文件：

```console
libman restore
```

## <a name="delete-library-files"></a>删除库文件

`libman clean` 命令删除之前通过 LibMan 还原的库文件。 删除此操作后变成空的文件夹。 不会删除*libman*的 `libraries` 属性中的库文件的关联配置。

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

* 从*libman*中的目标删除与指定库关联的所有文件。
* 从*libman*中删除关联的库配置。

出现错误时：

* 项目根目录中不存在*libman 文件。*
* 指定的库不存在。

如果安装了多个具有相同名称的库，系统将提示您选择一个。

### <a name="synopsis"></a>摘要

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>参数

`LIBRARY`

要卸载的库的名称。 此名称可以包含版本号表示法（例如 `@1.2.0`）。

### <a name="options"></a>选项

`libman uninstall` 命令可以使用以下选项：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

请考虑以下*libman*文件：

[!code-json[](samples/LibManSample/libman.json)]

* 若要卸载 jQuery，以下任一命令都将成功：

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* 卸载通过 `filesystem` 提供程序安装的 Lodash 等文件：

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>更新库版本

`libman update` 命令将通过 LibMan 安装的库更新到指定的版本。

出现错误时：

* 项目根目录中不存在*libman 文件。*
* 指定的库不存在。

如果安装了多个具有相同名称的库，系统将提示您选择一个。

### <a name="synopsis"></a>摘要

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>参数

`LIBRARY`

要更新的库的名称。

### <a name="options"></a>选项

`libman update` 命令可以使用以下选项：

* `-pre`

  获取库的最新预发布版本。

* `--to <VERSION>`

  获取库的特定版本。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

* 将 jQuery 更新为最新版本：

  ```console
  libman update jquery
  ```

* 若要将 jQuery 更新为版本3.3.1：

  ```console
  libman update jquery --to 3.3.1
  ```

* 若要将 jQuery 更新为最新的预发行版本：

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>管理库缓存

`libman cache` 命令管理 LibMan 库缓存。 `filesystem` 提供程序不使用库缓存。

### <a name="synopsis"></a>摘要

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>参数

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

* 若要查看每个提供程序的缓存库文件的名称，请执行以下操作：

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

* 若要清空 CDNJS 提供程序的库缓存：

  ```console
  libman cache clean cdnjs
  ```

  清空 CDNJS 提供程序缓存后，`libman cache list` 命令将显示以下内容：

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

  在清空所有提供程序缓存后，`libman cache list` 命令将显示以下内容：

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
