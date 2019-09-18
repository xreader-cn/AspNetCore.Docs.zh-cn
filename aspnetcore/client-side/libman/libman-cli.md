---
title: 将 LibMan 命令行接口（CLI）用于 ASP.NET Core
author: scottaddie
description: 了解如何在 ASP.NET Core 项目中使用 LibMan 命令行接口（CLI）。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: cf61bab2f0c3fc33d293968b8ac380cb56958d29
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080623"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a><span data-ttu-id="4c84d-103">将 LibMan 命令行接口（CLI）用于 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c84d-103">Use the LibMan command-line interface (CLI) with ASP.NET Core</span></span>

<span data-ttu-id="4c84d-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="4c84d-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="4c84d-105">[LibMan](xref:client-side/libman/index) CLI 是一个跨平台工具，在支持 .net Core 的任何地方都受支持。</span><span class="sxs-lookup"><span data-stu-id="4c84d-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4c84d-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="4c84d-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="4c84d-107">安装</span><span class="sxs-lookup"><span data-stu-id="4c84d-107">Installation</span></span>

<span data-ttu-id="4c84d-108">安装 LibMan CLI：</span><span class="sxs-lookup"><span data-stu-id="4c84d-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="4c84d-109">[.Net Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)是从[LibraryManager](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 包安装的。</span><span class="sxs-lookup"><span data-stu-id="4c84d-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="4c84d-110">若要从特定的 NuGet 包源安装 LibMan CLI，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="4c84d-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="4c84d-111">在前面的示例中，从本地 Windows 计算机的*C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg*文件安装 .Net Core 全局工具。</span><span class="sxs-lookup"><span data-stu-id="4c84d-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="4c84d-112">用法</span><span class="sxs-lookup"><span data-stu-id="4c84d-112">Usage</span></span>

<span data-ttu-id="4c84d-113">成功安装 CLI 后，可以使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="4c84d-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="4c84d-114">查看已安装的 CLI 版本：</span><span class="sxs-lookup"><span data-stu-id="4c84d-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="4c84d-115">查看可用的 CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="4c84d-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="4c84d-116">前面的命令显示类似于以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="4c84d-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="4c84d-117">以下部分概述了可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="4c84d-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="4c84d-118">在项目中初始化 LibMan</span><span class="sxs-lookup"><span data-stu-id="4c84d-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="4c84d-119">如果`libman init` *libman*文件不存在，则该命令将创建一个。</span><span class="sxs-lookup"><span data-stu-id="4c84d-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="4c84d-120">将用默认项模板内容创建文件。</span><span class="sxs-lookup"><span data-stu-id="4c84d-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="4c84d-121">摘要</span><span class="sxs-lookup"><span data-stu-id="4c84d-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="4c84d-122">选项</span><span class="sxs-lookup"><span data-stu-id="4c84d-122">Options</span></span>

<span data-ttu-id="4c84d-123">`libman init` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4c84d-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="4c84d-124">相对于当前文件夹的路径。</span><span class="sxs-lookup"><span data-stu-id="4c84d-124">A path relative to the current folder.</span></span> <span data-ttu-id="4c84d-125">如果未在*libman*中为库定义任何`destination`属性，则会在此位置安装库文件。</span><span class="sxs-lookup"><span data-stu-id="4c84d-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="4c84d-126">该值将写入`defaultDestination`到 libman 的属性。 `<PATH>`</span><span class="sxs-lookup"><span data-stu-id="4c84d-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="4c84d-127">为给定库定义了提供程序时要使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="4c84d-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="4c84d-128">该值将写入`defaultProvider`到 libman 的属性。 `<PROVIDER>`</span><span class="sxs-lookup"><span data-stu-id="4c84d-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="4c84d-129">替换`<PROVIDER>`为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="4c84d-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="4c84d-130">示例</span><span class="sxs-lookup"><span data-stu-id="4c84d-130">Examples</span></span>

<span data-ttu-id="4c84d-131">在 ASP.NET Core 项目中创建*libman*文件：</span><span class="sxs-lookup"><span data-stu-id="4c84d-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="4c84d-132">导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="4c84d-132">Navigate to the project root.</span></span>
* <span data-ttu-id="4c84d-133">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4c84d-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="4c84d-134">键入默认提供程序的名称，或按`Enter`使用默认的 CDNJS 提供程序。</span><span class="sxs-lookup"><span data-stu-id="4c84d-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="4c84d-135">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="4c84d-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 命令-默认提供程序](_static/libman-init-provider.png)

<span data-ttu-id="4c84d-137">将*libman*文件添加到项目根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="4c84d-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="4c84d-138">添加库文件</span><span class="sxs-lookup"><span data-stu-id="4c84d-138">Add library files</span></span>

<span data-ttu-id="4c84d-139">命令`libman install`将库文件下载并安装到项目中。</span><span class="sxs-lookup"><span data-stu-id="4c84d-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="4c84d-140">如果不存在*libman*文件，则添加一个。</span><span class="sxs-lookup"><span data-stu-id="4c84d-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="4c84d-141">修改*libman*文件以存储库文件的配置详细信息。</span><span class="sxs-lookup"><span data-stu-id="4c84d-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="4c84d-142">摘要</span><span class="sxs-lookup"><span data-stu-id="4c84d-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="4c84d-143">参数</span><span class="sxs-lookup"><span data-stu-id="4c84d-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="4c84d-144">要安装的库的名称。</span><span class="sxs-lookup"><span data-stu-id="4c84d-144">The name of the library to install.</span></span> <span data-ttu-id="4c84d-145">此名称可以包含版本号表示法（例如`@1.2.0`）。</span><span class="sxs-lookup"><span data-stu-id="4c84d-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="4c84d-146">选项</span><span class="sxs-lookup"><span data-stu-id="4c84d-146">Options</span></span>

<span data-ttu-id="4c84d-147">`libman install` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4c84d-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="4c84d-148">库的安装位置。</span><span class="sxs-lookup"><span data-stu-id="4c84d-148">The location to install the library.</span></span> <span data-ttu-id="4c84d-149">如果未指定，则使用默认位置。</span><span class="sxs-lookup"><span data-stu-id="4c84d-149">If not specified, the default location is used.</span></span> <span data-ttu-id="4c84d-150">如果 libman `defaultDestination`中未指定任何属性，则此选项是必需的。</span><span class="sxs-lookup"><span data-stu-id="4c84d-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="4c84d-151">指定要从库中安装的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="4c84d-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="4c84d-152">如果未指定，则会安装库中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="4c84d-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="4c84d-153">为每`--files`个要安装的文件提供一个选项。</span><span class="sxs-lookup"><span data-stu-id="4c84d-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="4c84d-154">还支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="4c84d-154">Relative paths are supported too.</span></span> <span data-ttu-id="4c84d-155">例如：`--files dist/browser/signalr.js`。</span><span class="sxs-lookup"><span data-stu-id="4c84d-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="4c84d-156">用于库获取的提供程序的名称。</span><span class="sxs-lookup"><span data-stu-id="4c84d-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="4c84d-157">替换`<PROVIDER>`为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="4c84d-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="4c84d-158">如果未指定，则`defaultProvider`使用*libman*中的属性。</span><span class="sxs-lookup"><span data-stu-id="4c84d-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="4c84d-159">如果 libman `defaultProvider`中未指定任何属性，则此选项是必需的。</span><span class="sxs-lookup"><span data-stu-id="4c84d-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="4c84d-160">示例</span><span class="sxs-lookup"><span data-stu-id="4c84d-160">Examples</span></span>

<span data-ttu-id="4c84d-161">请考虑以下*libman*文件：</span><span class="sxs-lookup"><span data-stu-id="4c84d-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="4c84d-162">使用 CDNJS 提供程序将 jQuery 版本 3.2.1 *jquery*文件安装到*wwwroot/scripts/jQuery*文件夹：</span><span class="sxs-lookup"><span data-stu-id="4c84d-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="4c84d-163">*Libman*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="4c84d-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="4c84d-164">使用文件系统提供程序从*C：\\temp\\\\ contosoCalendar*安装*node.js*和*calendar*文件：</span><span class="sxs-lookup"><span data-stu-id="4c84d-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="4c84d-165">出现以下两个原因的提示：</span><span class="sxs-lookup"><span data-stu-id="4c84d-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="4c84d-166">*Libman*文件不包含`defaultDestination`属性。</span><span class="sxs-lookup"><span data-stu-id="4c84d-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="4c84d-167">此`libman install`命令不`-d|--destination`包含选项。</span><span class="sxs-lookup"><span data-stu-id="4c84d-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman 安装命令-目标](_static/libman-install-destination.png)

<span data-ttu-id="4c84d-169">接受默认目标后， *libman*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="4c84d-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="4c84d-170">库将文件还原</span><span class="sxs-lookup"><span data-stu-id="4c84d-170">Restore library files</span></span>

<span data-ttu-id="4c84d-171">该`libman restore`命令将安装在*libman*中定义的库文件。</span><span class="sxs-lookup"><span data-stu-id="4c84d-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="4c84d-172">适用以下规则：</span><span class="sxs-lookup"><span data-stu-id="4c84d-172">The following rules apply:</span></span>

* <span data-ttu-id="4c84d-173">如果项目根目录中不存在*libman*文件，则会返回错误。</span><span class="sxs-lookup"><span data-stu-id="4c84d-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="4c84d-174">如果库指定提供程序，则忽略`defaultProvider` *libman*中的属性。</span><span class="sxs-lookup"><span data-stu-id="4c84d-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="4c84d-175">如果库指定一个目标，则将`defaultDestination`忽略*libman*中的属性。</span><span class="sxs-lookup"><span data-stu-id="4c84d-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="4c84d-176">摘要</span><span class="sxs-lookup"><span data-stu-id="4c84d-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="4c84d-177">选项</span><span class="sxs-lookup"><span data-stu-id="4c84d-177">Options</span></span>

<span data-ttu-id="4c84d-178">`libman restore` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4c84d-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="4c84d-179">示例</span><span class="sxs-lookup"><span data-stu-id="4c84d-179">Examples</span></span>

<span data-ttu-id="4c84d-180">若要还原*libman*中定义的库文件：</span><span class="sxs-lookup"><span data-stu-id="4c84d-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="4c84d-181">删除库文件</span><span class="sxs-lookup"><span data-stu-id="4c84d-181">Delete library files</span></span>

<span data-ttu-id="4c84d-182">`libman clean`命令删除以前通过 LibMan 还原的库文件。</span><span class="sxs-lookup"><span data-stu-id="4c84d-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="4c84d-183">删除此操作后变成空的文件夹。</span><span class="sxs-lookup"><span data-stu-id="4c84d-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="4c84d-184">不会删除`libraries` *libman*的属性中的库文件的关联配置。</span><span class="sxs-lookup"><span data-stu-id="4c84d-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="4c84d-185">摘要</span><span class="sxs-lookup"><span data-stu-id="4c84d-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="4c84d-186">选项</span><span class="sxs-lookup"><span data-stu-id="4c84d-186">Options</span></span>

<span data-ttu-id="4c84d-187">`libman clean` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4c84d-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="4c84d-188">示例</span><span class="sxs-lookup"><span data-stu-id="4c84d-188">Examples</span></span>

<span data-ttu-id="4c84d-189">删除通过 LibMan 安装的库文件：</span><span class="sxs-lookup"><span data-stu-id="4c84d-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="4c84d-190">卸载库文件</span><span class="sxs-lookup"><span data-stu-id="4c84d-190">Uninstall library files</span></span>

<span data-ttu-id="4c84d-191">`libman uninstall`命令：</span><span class="sxs-lookup"><span data-stu-id="4c84d-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="4c84d-192">从*libman*中的目标删除与指定库关联的所有文件。</span><span class="sxs-lookup"><span data-stu-id="4c84d-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="4c84d-193">从*libman*中删除关联的库配置。</span><span class="sxs-lookup"><span data-stu-id="4c84d-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="4c84d-194">出现错误时：</span><span class="sxs-lookup"><span data-stu-id="4c84d-194">An error occurs when:</span></span>

* <span data-ttu-id="4c84d-195">项目根目录中不存在*libman 文件。*</span><span class="sxs-lookup"><span data-stu-id="4c84d-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="4c84d-196">指定的库不存在。</span><span class="sxs-lookup"><span data-stu-id="4c84d-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="4c84d-197">如果安装了多个具有相同名称的库，系统将提示您选择一个。</span><span class="sxs-lookup"><span data-stu-id="4c84d-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="4c84d-198">摘要</span><span class="sxs-lookup"><span data-stu-id="4c84d-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="4c84d-199">参数</span><span class="sxs-lookup"><span data-stu-id="4c84d-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="4c84d-200">要卸载的库的名称。</span><span class="sxs-lookup"><span data-stu-id="4c84d-200">The name of the library to uninstall.</span></span> <span data-ttu-id="4c84d-201">此名称可以包含版本号表示法（例如`@1.2.0`）。</span><span class="sxs-lookup"><span data-stu-id="4c84d-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="4c84d-202">选项</span><span class="sxs-lookup"><span data-stu-id="4c84d-202">Options</span></span>

<span data-ttu-id="4c84d-203">`libman uninstall` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4c84d-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="4c84d-204">示例</span><span class="sxs-lookup"><span data-stu-id="4c84d-204">Examples</span></span>

<span data-ttu-id="4c84d-205">请考虑以下*libman*文件：</span><span class="sxs-lookup"><span data-stu-id="4c84d-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="4c84d-206">若要卸载 jQuery，以下任一命令都将成功：</span><span class="sxs-lookup"><span data-stu-id="4c84d-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="4c84d-207">卸载通过`filesystem`提供程序安装的 lodash 等文件：</span><span class="sxs-lookup"><span data-stu-id="4c84d-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="4c84d-208">更新库版本</span><span class="sxs-lookup"><span data-stu-id="4c84d-208">Update library version</span></span>

<span data-ttu-id="4c84d-209">`libman update`命令将通过 LibMan 安装的库更新到指定的版本。</span><span class="sxs-lookup"><span data-stu-id="4c84d-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="4c84d-210">出现错误时：</span><span class="sxs-lookup"><span data-stu-id="4c84d-210">An error occurs when:</span></span>

* <span data-ttu-id="4c84d-211">项目根目录中不存在*libman 文件。*</span><span class="sxs-lookup"><span data-stu-id="4c84d-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="4c84d-212">指定的库不存在。</span><span class="sxs-lookup"><span data-stu-id="4c84d-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="4c84d-213">如果安装了多个具有相同名称的库，系统将提示您选择一个。</span><span class="sxs-lookup"><span data-stu-id="4c84d-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="4c84d-214">摘要</span><span class="sxs-lookup"><span data-stu-id="4c84d-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="4c84d-215">参数</span><span class="sxs-lookup"><span data-stu-id="4c84d-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="4c84d-216">要更新的库的名称。</span><span class="sxs-lookup"><span data-stu-id="4c84d-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="4c84d-217">选项</span><span class="sxs-lookup"><span data-stu-id="4c84d-217">Options</span></span>

<span data-ttu-id="4c84d-218">`libman update` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4c84d-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="4c84d-219">获取库的最新预发布版本。</span><span class="sxs-lookup"><span data-stu-id="4c84d-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="4c84d-220">获取库的特定版本。</span><span class="sxs-lookup"><span data-stu-id="4c84d-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="4c84d-221">示例</span><span class="sxs-lookup"><span data-stu-id="4c84d-221">Examples</span></span>

* <span data-ttu-id="4c84d-222">将 jQuery 更新为最新版本：</span><span class="sxs-lookup"><span data-stu-id="4c84d-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="4c84d-223">若要将 jQuery 更新为版本3.3.1：</span><span class="sxs-lookup"><span data-stu-id="4c84d-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="4c84d-224">若要将 jQuery 更新为最新的预发行版本：</span><span class="sxs-lookup"><span data-stu-id="4c84d-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="4c84d-225">管理库缓存</span><span class="sxs-lookup"><span data-stu-id="4c84d-225">Manage library cache</span></span>

<span data-ttu-id="4c84d-226">`libman cache`命令管理 LibMan 库缓存。</span><span class="sxs-lookup"><span data-stu-id="4c84d-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="4c84d-227">`filesystem`提供程序不使用库缓存。</span><span class="sxs-lookup"><span data-stu-id="4c84d-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="4c84d-228">摘要</span><span class="sxs-lookup"><span data-stu-id="4c84d-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="4c84d-229">参数</span><span class="sxs-lookup"><span data-stu-id="4c84d-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="4c84d-230">仅与`clean`命令一起使用。</span><span class="sxs-lookup"><span data-stu-id="4c84d-230">Only used with the `clean` command.</span></span> <span data-ttu-id="4c84d-231">指定要清除的提供程序缓存。</span><span class="sxs-lookup"><span data-stu-id="4c84d-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="4c84d-232">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="4c84d-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="4c84d-233">选项</span><span class="sxs-lookup"><span data-stu-id="4c84d-233">Options</span></span>

<span data-ttu-id="4c84d-234">`libman cache` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="4c84d-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="4c84d-235">列出缓存的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="4c84d-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="4c84d-236">列出缓存的库的名称。</span><span class="sxs-lookup"><span data-stu-id="4c84d-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="4c84d-237">示例</span><span class="sxs-lookup"><span data-stu-id="4c84d-237">Examples</span></span>

* <span data-ttu-id="4c84d-238">若要查看每个提供程序的缓存库的名称，请使用以下命令之一：</span><span class="sxs-lookup"><span data-stu-id="4c84d-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="4c84d-239">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="4c84d-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="4c84d-240">若要查看每个提供程序的缓存库文件的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="4c84d-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="4c84d-241">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="4c84d-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="4c84d-242">请注意，前面的输出显示，jQuery 版本3.2.1 和3.3.1 缓存在 CDNJS 提供程序下。</span><span class="sxs-lookup"><span data-stu-id="4c84d-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="4c84d-243">若要清空 CDNJS 提供程序的库缓存：</span><span class="sxs-lookup"><span data-stu-id="4c84d-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="4c84d-244">清空 CDNJS 提供程序缓存后，该`libman cache list`命令会显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="4c84d-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="4c84d-245">为所有支持的提供程序清空缓存：</span><span class="sxs-lookup"><span data-stu-id="4c84d-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="4c84d-246">在清空所有提供程序缓存后`libman cache list` ，该命令会显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="4c84d-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="4c84d-247">其他资源</span><span class="sxs-lookup"><span data-stu-id="4c84d-247">Additional resources</span></span>

* [<span data-ttu-id="4c84d-248">安装全局工具</span><span class="sxs-lookup"><span data-stu-id="4c84d-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="4c84d-249">LibMan GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="4c84d-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
