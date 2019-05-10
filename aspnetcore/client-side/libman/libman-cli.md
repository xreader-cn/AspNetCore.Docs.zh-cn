---
title: ASP.NET Core 中使用 LibMan 命令行接口 (CLI)
author: scottaddie
description: 了解如何在 ASP.NET Core 项目中使用 LibMan 命令行接口 (CLI)。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: 5667f79648a60b8fd9496f8041ef08891ab766af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894754"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a>ASP.NET Core 中使用 LibMan 命令行接口 (CLI)

作者：[Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI 是具有支持的任何位置均支持.NET Core 的跨平台工具。

## <a name="prerequisites"></a>系统必备

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>安装

若要安装 LibMan CLI:

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

一个[.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)从安装[Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 包。

若要从特定的 NuGet 包源安装 LibMan CLI:

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

在前面的示例中，从本地 Windows 计算机的安装.NET Core 全局工具*C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg*文件。

## <a name="usage"></a>用法

安装成功后的 cli，可以使用以下命令：

```console
libman
```

若要查看已安装的 CLI 版本：

```console
libman --version
```

若要查看可用的 CLI 命令：

```console
libman --help
```

上述命令将显示类似于以下输出：

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

以下各节概述了可用的 CLI 命令。

## <a name="initialize-libman-in-the-project"></a>在项目中初始化 LibMan

`libman init`命令创建*libman.json*文件尚不存在。 使用默认项目模板内容创建该文件。

### <a name="synopsis"></a>摘要

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>选项

有以下选项`libman init`命令：

* `-d|--default-destination <PATH>`

  当前文件夹的相对路径。 如果不是库文件在此位置安装`destination`属性定义为库*libman.json*。 `<PATH>`值写入`defaultDestination`的属性*libman.json*。

* `-p|--default-provider <PROVIDER>`

  如果没有提供程序定义为给定库使用的提供程序。 `<PROVIDER>`值写入`defaultProvider`的属性*libman.json*。 替换为`<PROVIDER>`使用以下值之一：

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

若要创建*libman.json* ASP.NET Core 项目文件中：

* 导航到项目根目录。
* 运行下面的命令：

  ```console
  libman init
  ```

* 键入的默认提供程序或按名称`Enter`以使用默认 CDNJS 提供程序。 有效值包括：

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 命令的默认提供程序](_static/libman-init-provider.png)

一个*libman.json*文件添加到项目根目录包含以下内容：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>添加库文件

`libman install`命令下载和安装到项目的库文件。 一个*libman.json*如果不存在添加文件。 *Libman.json*修改文件来存储配置的库文件的详细信息。

### <a name="synopsis"></a>摘要

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>自变量

`LIBRARY`

若要安装的库的名称。 此名称可能包含版本的数字符号 (例如， `@1.2.0`)。

### <a name="options"></a>选项

有以下选项`libman install`命令：

* `-d|--destination <PATH>`

  要安装此库的位置。 如果未指定，使用默认位置。 如果没有`defaultDestination`属性中指定*libman.json*，则需要此选项。

* `--files <FILE>`

  指定要从库中安装的文件的名称。 如果未指定，安装库中的所有文件。 提供一个`--files`每个文件的选项来安装。 也支持相对路径。 例如：`--files dist/browser/signalr.js`。

* `-p|--provider <PROVIDER>`

  要用于库获取提供程序的名称。 替换为`<PROVIDER>`使用以下值之一：
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  如果未指定，否则`defaultProvider`中的属性*libman.json*使用。 如果没有`defaultProvider`属性中指定*libman.json*，则需要此选项。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

请考虑以下*libman.json*文件：

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

若要安装 jQuery 版本 3.2.1 *jquery.min.js*的文件*wwwroot/脚本/jquery*使用 CDNJS 提供程序的文件夹：

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

*Libman.json*文件如下所示：

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

若要安装*calendar.js*并*calendar.css*从文件*c:\\temp\\contosoCalendar\\* 使用文件系统提供程序：

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

以下提示将显示以下两个原因：

* *Libman.json*文件不包含`defaultDestination`属性。
* `libman install`命令不包含`-d|--destination`选项。

![libman 安装命令的目标](_static/libman-install-destination.png)

接受默认目标之后, *libman.json*文件如下所示：

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

`libman restore`命令安装库文件中定义*libman.json*。 适用以下规则：

* 如果没有*libman.json*项目根目录中存在文件，则返回错误。
* 如果库指定的提供程序，`defaultProvider`中的属性*libman.json*将被忽略。
* 如果库指定目标`defaultDestination`中的属性*libman.json*将被忽略。

### <a name="synopsis"></a>摘要

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>选项

有以下选项`libman restore`命令：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

若要还原中定义的库文件*libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>删除库文件

`libman clean`命令可删除以前还原通过 LibMan 库文件。 会删除此操作后，会为空的文件夹。 库文件的关联中的配置`libraries`的属性*libman.json*不会删除。

### <a name="synopsis"></a>摘要

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>选项

有以下选项`libman clean`命令：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

若要删除通过 LibMan 安装的库文件：

```console
libman clean
```

## <a name="uninstall-library-files"></a>卸载库文件

`libman uninstall`命令：

* 删除与指定的库中的目标从关联的所有文件*libman.json*。
* 将删除从关联的库配置*libman.json*。

发生错误时：

* 否*libman.json*项目根目录中存在的文件。
* 不存在指定的库。

如果安装多个具有相同名称的库，则系统会提示选择一个。

### <a name="synopsis"></a>摘要

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>自变量

`LIBRARY`

要卸载的库的名称。 此名称可能包含版本的数字符号 (例如， `@1.2.0`)。

### <a name="options"></a>选项

有以下选项`libman uninstall`命令：

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

请考虑以下*libman.json*文件：

[!code-json[](samples/LibManSample/libman.json)]

* 若要卸载 jQuery，以下命令之一成功：

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* 若要卸载 lodash 等文件通过安装`filesystem`提供程序：

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>更新库版本

`libman update`命令更新通过 LibMan 安装到指定版本的库。

发生错误时：

* 否*libman.json*项目根目录中存在的文件。
* 不存在指定的库。

如果安装多个具有相同名称的库，则系统会提示选择一个。

### <a name="synopsis"></a>摘要

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>自变量

`LIBRARY`

要更新的库的名称。

### <a name="options"></a>选项

有以下选项`libman update`命令：

* `-pre`

  获取库的最新的预发布版本。

* `--to <VERSION>`

  获取特定版本的库。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

* 若要更新到最新版本的 jQuery:

  ```console
  libman update jquery
  ```

* 若要更新到版本 3.3.1 jQuery:

  ```console
  libman update jquery --to 3.3.1
  ```

* 若要更新到最新的预发布版本的 jQuery:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>管理库缓存

`libman cache`命令管理 LibMan 库缓存。 `filesystem`提供程序不使用库缓存。

### <a name="synopsis"></a>摘要

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>自变量

`PROVIDER`

仅用于`clean`命令。 指定要清除缓存的提供程序。 有效值包括：

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>选项

有以下选项`libman cache`命令：

* `--files`

  列出缓存的文件的名称。

* `--libraries`

  列出的已缓存的库的名称。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>示例

* 若要查看的每个提供程序的缓存库的名称，请使用以下命令之一：

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

* 若要查看的每个提供程序的缓存的库文件的名称：

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

  请注意，上面的输出显示了在 CDNJS 提供程序缓存版本 3.2.1 和 3.3.1，jQuery。

* 若要在清空 CDNJS 提供程序库缓存：

  ```console
  libman cache clean cdnjs
  ```

  之后清空缓存的 CDNJS 提供程序`libman cache list`命令显示以下信息：

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

* 若要清空缓存的所有受支持的提供程序：

  ```console
  libman cache clean
  ```

  之后清空所有提供程序缓存，`libman cache list`命令显示以下信息：

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>其他资源

* [安装一个全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan GitHub 存储库](https://github.com/aspnet/LibraryManager)
