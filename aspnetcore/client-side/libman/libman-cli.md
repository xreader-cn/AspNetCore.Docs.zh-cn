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
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a><span data-ttu-id="048c6-103">ASP.NET Core 中使用 LibMan 命令行接口 (CLI)</span><span class="sxs-lookup"><span data-stu-id="048c6-103">Use the LibMan command-line interface (CLI) with ASP.NET Core</span></span>

<span data-ttu-id="048c6-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="048c6-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="048c6-105">[LibMan](xref:client-side/libman/index) CLI 是具有支持的任何位置均支持.NET Core 的跨平台工具。</span><span class="sxs-lookup"><span data-stu-id="048c6-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="048c6-106">系统必备</span><span class="sxs-lookup"><span data-stu-id="048c6-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="048c6-107">安装</span><span class="sxs-lookup"><span data-stu-id="048c6-107">Installation</span></span>

<span data-ttu-id="048c6-108">若要安装 LibMan CLI:</span><span class="sxs-lookup"><span data-stu-id="048c6-108">To install the LibMan CLI:</span></span>

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="048c6-109">一个[.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)从安装[Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="048c6-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="048c6-110">若要从特定的 NuGet 包源安装 LibMan CLI:</span><span class="sxs-lookup"><span data-stu-id="048c6-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="048c6-111">在前面的示例中，从本地 Windows 计算机的安装.NET Core 全局工具*C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg*文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="048c6-112">用法</span><span class="sxs-lookup"><span data-stu-id="048c6-112">Usage</span></span>

<span data-ttu-id="048c6-113">安装成功后的 cli，可以使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="048c6-114">若要查看已安装的 CLI 版本：</span><span class="sxs-lookup"><span data-stu-id="048c6-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="048c6-115">若要查看可用的 CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="048c6-116">上述命令将显示类似于以下输出：</span><span class="sxs-lookup"><span data-stu-id="048c6-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="048c6-117">以下各节概述了可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="048c6-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="048c6-118">在项目中初始化 LibMan</span><span class="sxs-lookup"><span data-stu-id="048c6-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="048c6-119">`libman init`命令创建*libman.json*文件尚不存在。</span><span class="sxs-lookup"><span data-stu-id="048c6-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="048c6-120">使用默认项目模板内容创建该文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="048c6-121">摘要</span><span class="sxs-lookup"><span data-stu-id="048c6-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="048c6-122">选项</span><span class="sxs-lookup"><span data-stu-id="048c6-122">Options</span></span>

<span data-ttu-id="048c6-123">有以下选项`libman init`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="048c6-124">当前文件夹的相对路径。</span><span class="sxs-lookup"><span data-stu-id="048c6-124">A path relative to the current folder.</span></span> <span data-ttu-id="048c6-125">如果不是库文件在此位置安装`destination`属性定义为库*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="048c6-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="048c6-126">`<PATH>`值写入`defaultDestination`的属性*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="048c6-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="048c6-127">如果没有提供程序定义为给定库使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="048c6-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="048c6-128">`<PROVIDER>`值写入`defaultProvider`的属性*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="048c6-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="048c6-129">替换为`<PROVIDER>`使用以下值之一：</span><span class="sxs-lookup"><span data-stu-id="048c6-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="048c6-130">示例</span><span class="sxs-lookup"><span data-stu-id="048c6-130">Examples</span></span>

<span data-ttu-id="048c6-131">若要创建*libman.json* ASP.NET Core 项目文件中：</span><span class="sxs-lookup"><span data-stu-id="048c6-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="048c6-132">导航到项目根目录。</span><span class="sxs-lookup"><span data-stu-id="048c6-132">Navigate to the project root.</span></span>
* <span data-ttu-id="048c6-133">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="048c6-134">键入的默认提供程序或按名称`Enter`以使用默认 CDNJS 提供程序。</span><span class="sxs-lookup"><span data-stu-id="048c6-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="048c6-135">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="048c6-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 命令的默认提供程序](_static/libman-init-provider.png)

<span data-ttu-id="048c6-137">一个*libman.json*文件添加到项目根目录包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="048c6-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="048c6-138">添加库文件</span><span class="sxs-lookup"><span data-stu-id="048c6-138">Add library files</span></span>

<span data-ttu-id="048c6-139">`libman install`命令下载和安装到项目的库文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="048c6-140">一个*libman.json*如果不存在添加文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="048c6-141">*Libman.json*修改文件来存储配置的库文件的详细信息。</span><span class="sxs-lookup"><span data-stu-id="048c6-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="048c6-142">摘要</span><span class="sxs-lookup"><span data-stu-id="048c6-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="048c6-143">自变量</span><span class="sxs-lookup"><span data-stu-id="048c6-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="048c6-144">若要安装的库的名称。</span><span class="sxs-lookup"><span data-stu-id="048c6-144">The name of the library to install.</span></span> <span data-ttu-id="048c6-145">此名称可能包含版本的数字符号 (例如， `@1.2.0`)。</span><span class="sxs-lookup"><span data-stu-id="048c6-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="048c6-146">选项</span><span class="sxs-lookup"><span data-stu-id="048c6-146">Options</span></span>

<span data-ttu-id="048c6-147">有以下选项`libman install`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="048c6-148">要安装此库的位置。</span><span class="sxs-lookup"><span data-stu-id="048c6-148">The location to install the library.</span></span> <span data-ttu-id="048c6-149">如果未指定，使用默认位置。</span><span class="sxs-lookup"><span data-stu-id="048c6-149">If not specified, the default location is used.</span></span> <span data-ttu-id="048c6-150">如果没有`defaultDestination`属性中指定*libman.json*，则需要此选项。</span><span class="sxs-lookup"><span data-stu-id="048c6-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="048c6-151">指定要从库中安装的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="048c6-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="048c6-152">如果未指定，安装库中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="048c6-153">提供一个`--files`每个文件的选项来安装。</span><span class="sxs-lookup"><span data-stu-id="048c6-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="048c6-154">也支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="048c6-154">Relative paths are supported too.</span></span> <span data-ttu-id="048c6-155">例如：`--files dist/browser/signalr.js`。</span><span class="sxs-lookup"><span data-stu-id="048c6-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="048c6-156">要用于库获取提供程序的名称。</span><span class="sxs-lookup"><span data-stu-id="048c6-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="048c6-157">替换为`<PROVIDER>`使用以下值之一：</span><span class="sxs-lookup"><span data-stu-id="048c6-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="048c6-158">如果未指定，否则`defaultProvider`中的属性*libman.json*使用。</span><span class="sxs-lookup"><span data-stu-id="048c6-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="048c6-159">如果没有`defaultProvider`属性中指定*libman.json*，则需要此选项。</span><span class="sxs-lookup"><span data-stu-id="048c6-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="048c6-160">示例</span><span class="sxs-lookup"><span data-stu-id="048c6-160">Examples</span></span>

<span data-ttu-id="048c6-161">请考虑以下*libman.json*文件：</span><span class="sxs-lookup"><span data-stu-id="048c6-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="048c6-162">若要安装 jQuery 版本 3.2.1 *jquery.min.js*的文件*wwwroot/脚本/jquery*使用 CDNJS 提供程序的文件夹：</span><span class="sxs-lookup"><span data-stu-id="048c6-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="048c6-163">*Libman.json*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="048c6-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="048c6-164">若要安装*calendar.js*并*calendar.css*从文件*c:\\temp\\contosoCalendar\\* 使用文件系统提供程序：</span><span class="sxs-lookup"><span data-stu-id="048c6-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="048c6-165">以下提示将显示以下两个原因：</span><span class="sxs-lookup"><span data-stu-id="048c6-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="048c6-166">*Libman.json*文件不包含`defaultDestination`属性。</span><span class="sxs-lookup"><span data-stu-id="048c6-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="048c6-167">`libman install`命令不包含`-d|--destination`选项。</span><span class="sxs-lookup"><span data-stu-id="048c6-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman 安装命令的目标](_static/libman-install-destination.png)

<span data-ttu-id="048c6-169">接受默认目标之后, *libman.json*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="048c6-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="048c6-170">库将文件还原</span><span class="sxs-lookup"><span data-stu-id="048c6-170">Restore library files</span></span>

<span data-ttu-id="048c6-171">`libman restore`命令安装库文件中定义*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="048c6-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="048c6-172">适用以下规则：</span><span class="sxs-lookup"><span data-stu-id="048c6-172">The following rules apply:</span></span>

* <span data-ttu-id="048c6-173">如果没有*libman.json*项目根目录中存在文件，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="048c6-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="048c6-174">如果库指定的提供程序，`defaultProvider`中的属性*libman.json*将被忽略。</span><span class="sxs-lookup"><span data-stu-id="048c6-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="048c6-175">如果库指定目标`defaultDestination`中的属性*libman.json*将被忽略。</span><span class="sxs-lookup"><span data-stu-id="048c6-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="048c6-176">摘要</span><span class="sxs-lookup"><span data-stu-id="048c6-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="048c6-177">选项</span><span class="sxs-lookup"><span data-stu-id="048c6-177">Options</span></span>

<span data-ttu-id="048c6-178">有以下选项`libman restore`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="048c6-179">示例</span><span class="sxs-lookup"><span data-stu-id="048c6-179">Examples</span></span>

<span data-ttu-id="048c6-180">若要还原中定义的库文件*libman.json*:</span><span class="sxs-lookup"><span data-stu-id="048c6-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="048c6-181">删除库文件</span><span class="sxs-lookup"><span data-stu-id="048c6-181">Delete library files</span></span>

<span data-ttu-id="048c6-182">`libman clean`命令可删除以前还原通过 LibMan 库文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="048c6-183">会删除此操作后，会为空的文件夹。</span><span class="sxs-lookup"><span data-stu-id="048c6-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="048c6-184">库文件的关联中的配置`libraries`的属性*libman.json*不会删除。</span><span class="sxs-lookup"><span data-stu-id="048c6-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="048c6-185">摘要</span><span class="sxs-lookup"><span data-stu-id="048c6-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="048c6-186">选项</span><span class="sxs-lookup"><span data-stu-id="048c6-186">Options</span></span>

<span data-ttu-id="048c6-187">有以下选项`libman clean`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="048c6-188">示例</span><span class="sxs-lookup"><span data-stu-id="048c6-188">Examples</span></span>

<span data-ttu-id="048c6-189">若要删除通过 LibMan 安装的库文件：</span><span class="sxs-lookup"><span data-stu-id="048c6-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="048c6-190">卸载库文件</span><span class="sxs-lookup"><span data-stu-id="048c6-190">Uninstall library files</span></span>

<span data-ttu-id="048c6-191">`libman uninstall`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="048c6-192">删除与指定的库中的目标从关联的所有文件*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="048c6-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="048c6-193">将删除从关联的库配置*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="048c6-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="048c6-194">发生错误时：</span><span class="sxs-lookup"><span data-stu-id="048c6-194">An error occurs when:</span></span>

* <span data-ttu-id="048c6-195">否*libman.json*项目根目录中存在的文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="048c6-196">不存在指定的库。</span><span class="sxs-lookup"><span data-stu-id="048c6-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="048c6-197">如果安装多个具有相同名称的库，则系统会提示选择一个。</span><span class="sxs-lookup"><span data-stu-id="048c6-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="048c6-198">摘要</span><span class="sxs-lookup"><span data-stu-id="048c6-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="048c6-199">自变量</span><span class="sxs-lookup"><span data-stu-id="048c6-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="048c6-200">要卸载的库的名称。</span><span class="sxs-lookup"><span data-stu-id="048c6-200">The name of the library to uninstall.</span></span> <span data-ttu-id="048c6-201">此名称可能包含版本的数字符号 (例如， `@1.2.0`)。</span><span class="sxs-lookup"><span data-stu-id="048c6-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="048c6-202">选项</span><span class="sxs-lookup"><span data-stu-id="048c6-202">Options</span></span>

<span data-ttu-id="048c6-203">有以下选项`libman uninstall`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="048c6-204">示例</span><span class="sxs-lookup"><span data-stu-id="048c6-204">Examples</span></span>

<span data-ttu-id="048c6-205">请考虑以下*libman.json*文件：</span><span class="sxs-lookup"><span data-stu-id="048c6-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="048c6-206">若要卸载 jQuery，以下命令之一成功：</span><span class="sxs-lookup"><span data-stu-id="048c6-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="048c6-207">若要卸载 lodash 等文件通过安装`filesystem`提供程序：</span><span class="sxs-lookup"><span data-stu-id="048c6-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="048c6-208">更新库版本</span><span class="sxs-lookup"><span data-stu-id="048c6-208">Update library version</span></span>

<span data-ttu-id="048c6-209">`libman update`命令更新通过 LibMan 安装到指定版本的库。</span><span class="sxs-lookup"><span data-stu-id="048c6-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="048c6-210">发生错误时：</span><span class="sxs-lookup"><span data-stu-id="048c6-210">An error occurs when:</span></span>

* <span data-ttu-id="048c6-211">否*libman.json*项目根目录中存在的文件。</span><span class="sxs-lookup"><span data-stu-id="048c6-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="048c6-212">不存在指定的库。</span><span class="sxs-lookup"><span data-stu-id="048c6-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="048c6-213">如果安装多个具有相同名称的库，则系统会提示选择一个。</span><span class="sxs-lookup"><span data-stu-id="048c6-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="048c6-214">摘要</span><span class="sxs-lookup"><span data-stu-id="048c6-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="048c6-215">自变量</span><span class="sxs-lookup"><span data-stu-id="048c6-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="048c6-216">要更新的库的名称。</span><span class="sxs-lookup"><span data-stu-id="048c6-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="048c6-217">选项</span><span class="sxs-lookup"><span data-stu-id="048c6-217">Options</span></span>

<span data-ttu-id="048c6-218">有以下选项`libman update`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="048c6-219">获取库的最新的预发布版本。</span><span class="sxs-lookup"><span data-stu-id="048c6-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="048c6-220">获取特定版本的库。</span><span class="sxs-lookup"><span data-stu-id="048c6-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="048c6-221">示例</span><span class="sxs-lookup"><span data-stu-id="048c6-221">Examples</span></span>

* <span data-ttu-id="048c6-222">若要更新到最新版本的 jQuery:</span><span class="sxs-lookup"><span data-stu-id="048c6-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="048c6-223">若要更新到版本 3.3.1 jQuery:</span><span class="sxs-lookup"><span data-stu-id="048c6-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="048c6-224">若要更新到最新的预发布版本的 jQuery:</span><span class="sxs-lookup"><span data-stu-id="048c6-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="048c6-225">管理库缓存</span><span class="sxs-lookup"><span data-stu-id="048c6-225">Manage library cache</span></span>

<span data-ttu-id="048c6-226">`libman cache`命令管理 LibMan 库缓存。</span><span class="sxs-lookup"><span data-stu-id="048c6-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="048c6-227">`filesystem`提供程序不使用库缓存。</span><span class="sxs-lookup"><span data-stu-id="048c6-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="048c6-228">摘要</span><span class="sxs-lookup"><span data-stu-id="048c6-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="048c6-229">自变量</span><span class="sxs-lookup"><span data-stu-id="048c6-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="048c6-230">仅用于`clean`命令。</span><span class="sxs-lookup"><span data-stu-id="048c6-230">Only used with the `clean` command.</span></span> <span data-ttu-id="048c6-231">指定要清除缓存的提供程序。</span><span class="sxs-lookup"><span data-stu-id="048c6-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="048c6-232">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="048c6-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="048c6-233">选项</span><span class="sxs-lookup"><span data-stu-id="048c6-233">Options</span></span>

<span data-ttu-id="048c6-234">有以下选项`libman cache`命令：</span><span class="sxs-lookup"><span data-stu-id="048c6-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="048c6-235">列出缓存的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="048c6-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="048c6-236">列出的已缓存的库的名称。</span><span class="sxs-lookup"><span data-stu-id="048c6-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="048c6-237">示例</span><span class="sxs-lookup"><span data-stu-id="048c6-237">Examples</span></span>

* <span data-ttu-id="048c6-238">若要查看的每个提供程序的缓存库的名称，请使用以下命令之一：</span><span class="sxs-lookup"><span data-stu-id="048c6-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="048c6-239">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="048c6-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="048c6-240">若要查看的每个提供程序的缓存的库文件的名称：</span><span class="sxs-lookup"><span data-stu-id="048c6-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="048c6-241">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="048c6-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="048c6-242">请注意，上面的输出显示了在 CDNJS 提供程序缓存版本 3.2.1 和 3.3.1，jQuery。</span><span class="sxs-lookup"><span data-stu-id="048c6-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="048c6-243">若要在清空 CDNJS 提供程序库缓存：</span><span class="sxs-lookup"><span data-stu-id="048c6-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="048c6-244">之后清空缓存的 CDNJS 提供程序`libman cache list`命令显示以下信息：</span><span class="sxs-lookup"><span data-stu-id="048c6-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="048c6-245">若要清空缓存的所有受支持的提供程序：</span><span class="sxs-lookup"><span data-stu-id="048c6-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="048c6-246">之后清空所有提供程序缓存，`libman cache list`命令显示以下信息：</span><span class="sxs-lookup"><span data-stu-id="048c6-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="048c6-247">其他资源</span><span class="sxs-lookup"><span data-stu-id="048c6-247">Additional resources</span></span>

* [<span data-ttu-id="048c6-248">安装一个全局工具</span><span class="sxs-lookup"><span data-stu-id="048c6-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="048c6-249">LibMan GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="048c6-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
