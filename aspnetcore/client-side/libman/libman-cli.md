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
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="ea1b3-103">将 LibMan CLI 与 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea1b3-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="ea1b3-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="ea1b3-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="ea1b3-105">[LibMan](xref:client-side/libman/index) CLI 是一个跨平台工具，在支持 .net Core 的任何地方都受支持。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ea1b3-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="ea1b3-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="ea1b3-107">安装</span><span class="sxs-lookup"><span data-stu-id="ea1b3-107">Installation</span></span>

<span data-ttu-id="ea1b3-108">安装 LibMan CLI：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="ea1b3-109">[.Net Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)是从[LibraryManager](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 包安装的。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="ea1b3-110">若要从特定的 NuGet 包源安装 LibMan CLI，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="ea1b3-111">在前面的示例中，从本地 Windows 计算机的*C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg*文件安装 .Net Core 全局工具。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="ea1b3-112">用量</span><span class="sxs-lookup"><span data-stu-id="ea1b3-112">Usage</span></span>

<span data-ttu-id="ea1b3-113">成功安装 CLI 后，可以使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="ea1b3-114">查看已安装的 CLI 版本：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="ea1b3-115">查看可用的 CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="ea1b3-116">前面的命令显示类似于以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="ea1b3-117">以下部分概述了可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="ea1b3-118">在项目中初始化 LibMan</span><span class="sxs-lookup"><span data-stu-id="ea1b3-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="ea1b3-119">`libman init` 命令将创建一个*libman*文件（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="ea1b3-120">将用默认项模板内容创建文件。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="ea1b3-121">摘要</span><span class="sxs-lookup"><span data-stu-id="ea1b3-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="ea1b3-122">选项</span><span class="sxs-lookup"><span data-stu-id="ea1b3-122">Options</span></span>

<span data-ttu-id="ea1b3-123">`libman init` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="ea1b3-124">相对于当前文件夹的路径。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-124">A path relative to the current folder.</span></span> <span data-ttu-id="ea1b3-125">如果未在*libman*中为库定义 `destination` 属性，则会在此位置安装库文件。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="ea1b3-126">`<PATH>` 值将写入*libman*的 `defaultDestination` 属性。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="ea1b3-127">为给定库定义了提供程序时要使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="ea1b3-128">`<PROVIDER>` 值将写入*libman*的 `defaultProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="ea1b3-129">将 `<PROVIDER>` 替换为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="ea1b3-130">示例</span><span class="sxs-lookup"><span data-stu-id="ea1b3-130">Examples</span></span>

<span data-ttu-id="ea1b3-131">在 ASP.NET Core 项目中创建*libman*文件：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="ea1b3-132">导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-132">Navigate to the project root.</span></span>
* <span data-ttu-id="ea1b3-133">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="ea1b3-134">键入默认提供程序的名称，或按 `Enter` 使用默认的 CDNJS 提供程序。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="ea1b3-135">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 命令-默认提供程序](_static/libman-init-provider.png)

<span data-ttu-id="ea1b3-137">将*libman*文件添加到项目根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="ea1b3-138">添加库文件</span><span class="sxs-lookup"><span data-stu-id="ea1b3-138">Add library files</span></span>

<span data-ttu-id="ea1b3-139">`libman install` 命令将库文件下载并安装到项目中。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="ea1b3-140">如果不存在*libman*文件，则添加一个。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="ea1b3-141">修改*libman*文件以存储库文件的配置详细信息。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="ea1b3-142">摘要</span><span class="sxs-lookup"><span data-stu-id="ea1b3-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="ea1b3-143">参数</span><span class="sxs-lookup"><span data-stu-id="ea1b3-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="ea1b3-144">要安装的库的名称。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-144">The name of the library to install.</span></span> <span data-ttu-id="ea1b3-145">此名称可以包含版本号表示法（例如 `@1.2.0`）。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="ea1b3-146">选项</span><span class="sxs-lookup"><span data-stu-id="ea1b3-146">Options</span></span>

<span data-ttu-id="ea1b3-147">`libman install` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="ea1b3-148">库的安装位置。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-148">The location to install the library.</span></span> <span data-ttu-id="ea1b3-149">如果未指定，则使用默认位置。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-149">If not specified, the default location is used.</span></span> <span data-ttu-id="ea1b3-150">如果*libman*中未指定 `defaultDestination` 属性，则此选项是必需的。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="ea1b3-151">指定要从库中安装的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="ea1b3-152">如果未指定，则会安装库中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="ea1b3-153">为每个要安装的文件提供一个 `--files` 选项。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="ea1b3-154">还支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-154">Relative paths are supported too.</span></span> <span data-ttu-id="ea1b3-155">例如：`--files dist/browser/signalr.js`。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="ea1b3-156">用于库获取的提供程序的名称。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="ea1b3-157">将 `<PROVIDER>` 替换为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="ea1b3-158">如果未指定，则使用*libman*中的 `defaultProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="ea1b3-159">如果*libman*中未指定 `defaultProvider` 属性，则此选项是必需的。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="ea1b3-160">示例</span><span class="sxs-lookup"><span data-stu-id="ea1b3-160">Examples</span></span>

<span data-ttu-id="ea1b3-161">请考虑以下*libman*文件：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="ea1b3-162">使用 CDNJS 提供程序将 jQuery 版本 3.2.1 *jquery*文件安装到*wwwroot/scripts/jQuery*文件夹：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="ea1b3-163">*Libman*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="ea1b3-164">使用文件系统提供程序从*C：\\temp\\contosoCalendar\\* 中安装*node.js*和*calendar*文件：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="ea1b3-165">出现以下两个原因的提示：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="ea1b3-166">*Libman*文件不包含 `defaultDestination` 属性。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="ea1b3-167">`libman install` 命令不包含 `-d|--destination` 选项。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman 安装命令-目标](_static/libman-install-destination.png)

<span data-ttu-id="ea1b3-169">接受默认目标后， *libman*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="ea1b3-170">库将文件还原</span><span class="sxs-lookup"><span data-stu-id="ea1b3-170">Restore library files</span></span>

<span data-ttu-id="ea1b3-171">`libman restore` 命令安装在*libman*中定义的库文件。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="ea1b3-172">适用以下规则：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-172">The following rules apply:</span></span>

* <span data-ttu-id="ea1b3-173">如果项目根目录中不存在*libman*文件，则会返回错误。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="ea1b3-174">如果库指定提供程序，则会忽略*libman*中的 `defaultProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="ea1b3-175">如果库指定一个目标，则将忽略*libman*中的 `defaultDestination` 属性。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="ea1b3-176">摘要</span><span class="sxs-lookup"><span data-stu-id="ea1b3-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="ea1b3-177">选项</span><span class="sxs-lookup"><span data-stu-id="ea1b3-177">Options</span></span>

<span data-ttu-id="ea1b3-178">`libman restore` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="ea1b3-179">示例</span><span class="sxs-lookup"><span data-stu-id="ea1b3-179">Examples</span></span>

<span data-ttu-id="ea1b3-180">若要还原*libman*中定义的库文件：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="ea1b3-181">删除库文件</span><span class="sxs-lookup"><span data-stu-id="ea1b3-181">Delete library files</span></span>

<span data-ttu-id="ea1b3-182">`libman clean` 命令删除之前通过 LibMan 还原的库文件。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="ea1b3-183">删除此操作后变成空的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="ea1b3-184">不会删除*libman*的 `libraries` 属性中的库文件的关联配置。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="ea1b3-185">摘要</span><span class="sxs-lookup"><span data-stu-id="ea1b3-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="ea1b3-186">选项</span><span class="sxs-lookup"><span data-stu-id="ea1b3-186">Options</span></span>

<span data-ttu-id="ea1b3-187">`libman clean` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="ea1b3-188">示例</span><span class="sxs-lookup"><span data-stu-id="ea1b3-188">Examples</span></span>

<span data-ttu-id="ea1b3-189">删除通过 LibMan 安装的库文件：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="ea1b3-190">卸载库文件</span><span class="sxs-lookup"><span data-stu-id="ea1b3-190">Uninstall library files</span></span>

<span data-ttu-id="ea1b3-191">`libman uninstall` 命令：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="ea1b3-192">从*libman*中的目标删除与指定库关联的所有文件。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="ea1b3-193">从*libman*中删除关联的库配置。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="ea1b3-194">出现错误时：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-194">An error occurs when:</span></span>

* <span data-ttu-id="ea1b3-195">项目根目录中不存在*libman 文件。*</span><span class="sxs-lookup"><span data-stu-id="ea1b3-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="ea1b3-196">指定的库不存在。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="ea1b3-197">如果安装了多个具有相同名称的库，系统将提示您选择一个。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="ea1b3-198">摘要</span><span class="sxs-lookup"><span data-stu-id="ea1b3-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="ea1b3-199">参数</span><span class="sxs-lookup"><span data-stu-id="ea1b3-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="ea1b3-200">要卸载的库的名称。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-200">The name of the library to uninstall.</span></span> <span data-ttu-id="ea1b3-201">此名称可以包含版本号表示法（例如 `@1.2.0`）。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="ea1b3-202">选项</span><span class="sxs-lookup"><span data-stu-id="ea1b3-202">Options</span></span>

<span data-ttu-id="ea1b3-203">`libman uninstall` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="ea1b3-204">示例</span><span class="sxs-lookup"><span data-stu-id="ea1b3-204">Examples</span></span>

<span data-ttu-id="ea1b3-205">请考虑以下*libman*文件：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="ea1b3-206">若要卸载 jQuery，以下任一命令都将成功：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="ea1b3-207">卸载通过 `filesystem` 提供程序安装的 Lodash 等文件：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="ea1b3-208">更新库版本</span><span class="sxs-lookup"><span data-stu-id="ea1b3-208">Update library version</span></span>

<span data-ttu-id="ea1b3-209">`libman update` 命令将通过 LibMan 安装的库更新到指定的版本。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="ea1b3-210">出现错误时：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-210">An error occurs when:</span></span>

* <span data-ttu-id="ea1b3-211">项目根目录中不存在*libman 文件。*</span><span class="sxs-lookup"><span data-stu-id="ea1b3-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="ea1b3-212">指定的库不存在。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="ea1b3-213">如果安装了多个具有相同名称的库，系统将提示您选择一个。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="ea1b3-214">摘要</span><span class="sxs-lookup"><span data-stu-id="ea1b3-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="ea1b3-215">参数</span><span class="sxs-lookup"><span data-stu-id="ea1b3-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="ea1b3-216">要更新的库的名称。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="ea1b3-217">选项</span><span class="sxs-lookup"><span data-stu-id="ea1b3-217">Options</span></span>

<span data-ttu-id="ea1b3-218">`libman update` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="ea1b3-219">获取库的最新预发布版本。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="ea1b3-220">获取库的特定版本。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="ea1b3-221">示例</span><span class="sxs-lookup"><span data-stu-id="ea1b3-221">Examples</span></span>

* <span data-ttu-id="ea1b3-222">将 jQuery 更新为最新版本：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="ea1b3-223">若要将 jQuery 更新为版本3.3.1：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="ea1b3-224">若要将 jQuery 更新为最新的预发行版本：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="ea1b3-225">管理库缓存</span><span class="sxs-lookup"><span data-stu-id="ea1b3-225">Manage library cache</span></span>

<span data-ttu-id="ea1b3-226">`libman cache` 命令管理 LibMan 库缓存。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="ea1b3-227">`filesystem` 提供程序不使用库缓存。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="ea1b3-228">摘要</span><span class="sxs-lookup"><span data-stu-id="ea1b3-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="ea1b3-229">参数</span><span class="sxs-lookup"><span data-stu-id="ea1b3-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="ea1b3-230">仅与 `clean` 命令一起使用。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-230">Only used with the `clean` command.</span></span> <span data-ttu-id="ea1b3-231">指定要清除的提供程序缓存。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="ea1b3-232">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="ea1b3-233">选项</span><span class="sxs-lookup"><span data-stu-id="ea1b3-233">Options</span></span>

<span data-ttu-id="ea1b3-234">`libman cache` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="ea1b3-235">列出缓存的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="ea1b3-236">列出缓存的库的名称。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="ea1b3-237">示例</span><span class="sxs-lookup"><span data-stu-id="ea1b3-237">Examples</span></span>

* <span data-ttu-id="ea1b3-238">若要查看每个提供程序的缓存库的名称，请使用以下命令之一：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="ea1b3-239">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="ea1b3-240">若要查看每个提供程序的缓存库文件的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="ea1b3-241">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="ea1b3-242">请注意，前面的输出显示，jQuery 版本3.2.1 和3.3.1 缓存在 CDNJS 提供程序下。</span><span class="sxs-lookup"><span data-stu-id="ea1b3-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="ea1b3-243">若要清空 CDNJS 提供程序的库缓存：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="ea1b3-244">清空 CDNJS 提供程序缓存后，`libman cache list` 命令将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="ea1b3-245">为所有支持的提供程序清空缓存：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="ea1b3-246">在清空所有提供程序缓存后，`libman cache list` 命令将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="ea1b3-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="ea1b3-247">其他资源</span><span class="sxs-lookup"><span data-stu-id="ea1b3-247">Additional resources</span></span>

* [<span data-ttu-id="ea1b3-248">安装全局工具</span><span class="sxs-lookup"><span data-stu-id="ea1b3-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="ea1b3-249">LibMan GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="ea1b3-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
