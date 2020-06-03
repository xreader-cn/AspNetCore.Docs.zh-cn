---
title: 将 LibMan CLI 与 ASP.NET Core 结合使用
author: scottaddie
description: 了解如何在 ASP.NET Core 项目中使用 LibMan CLI。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 1a42d162e28d4bb4cce284b8b5e37f1be6ff64c6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770547"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="e6f73-103">将 LibMan CLI 与 ASP.NET Core 结合使用</span><span class="sxs-lookup"><span data-stu-id="e6f73-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="e6f73-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e6f73-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e6f73-105">[LibMan](xref:client-side/libman/index) CLI 是一种跨平台工具，在所有支持的 .NET Core 的位置都可得到支持。</span><span class="sxs-lookup"><span data-stu-id="e6f73-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6f73-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="e6f73-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="e6f73-107">安装</span><span class="sxs-lookup"><span data-stu-id="e6f73-107">Installation</span></span>

<span data-ttu-id="e6f73-108">安装 LibMan CLI：</span><span class="sxs-lookup"><span data-stu-id="e6f73-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="e6f73-109">从 [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 包安装 [.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)。</span><span class="sxs-lookup"><span data-stu-id="e6f73-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="e6f73-110">从特定 NuGet 包源安装 LibMan CLI：</span><span class="sxs-lookup"><span data-stu-id="e6f73-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="e6f73-111">在前面的示例中，从本地 Windows 计算机的 C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg 文件安装 .NET Core 全局工具。</span><span class="sxs-lookup"><span data-stu-id="e6f73-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="e6f73-112">用法</span><span class="sxs-lookup"><span data-stu-id="e6f73-112">Usage</span></span>

<span data-ttu-id="e6f73-113">成功安装 CLI 之后，可以使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="e6f73-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="e6f73-114">查看已安装的 CLI 版本：</span><span class="sxs-lookup"><span data-stu-id="e6f73-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="e6f73-115">查看可用 CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="e6f73-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="e6f73-116">前面的命令显示类似于以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="e6f73-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="e6f73-117">以下部分概述了可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="e6f73-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="e6f73-118">在项目中初始化 LibMan</span><span class="sxs-lookup"><span data-stu-id="e6f73-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="e6f73-119">`libman init` 命令会创建一个 libman.json 文件（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="e6f73-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="e6f73-120">该文件会使用默认项模板内容进行创建。</span><span class="sxs-lookup"><span data-stu-id="e6f73-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e6f73-121">摘要</span><span class="sxs-lookup"><span data-stu-id="e6f73-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e6f73-122">选项</span><span class="sxs-lookup"><span data-stu-id="e6f73-122">Options</span></span>

<span data-ttu-id="e6f73-123">`libman init` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="e6f73-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="e6f73-124">相对于当前文件夹的路径。</span><span class="sxs-lookup"><span data-stu-id="e6f73-124">A path relative to the current folder.</span></span> <span data-ttu-id="e6f73-125">如果未在 libman.json 中为库定义 `destination` 属性，则库文件会安装在此位置。</span><span class="sxs-lookup"><span data-stu-id="e6f73-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="e6f73-126">`<PATH>` 值会写入 libman.json 的 `defaultDestination` 属性。</span><span class="sxs-lookup"><span data-stu-id="e6f73-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="e6f73-127">未为给定库定义提供程序时要使用的提供程序。</span><span class="sxs-lookup"><span data-stu-id="e6f73-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="e6f73-128">`<PROVIDER>` 值会写入 libman.json 的 `defaultProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="e6f73-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="e6f73-129">将 `<PROVIDER>` 替换为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="e6f73-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e6f73-130">示例</span><span class="sxs-lookup"><span data-stu-id="e6f73-130">Examples</span></span>

<span data-ttu-id="e6f73-131">在 ASP.NET Core 项目中创建 libman.json 文件：</span><span class="sxs-lookup"><span data-stu-id="e6f73-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="e6f73-132">导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="e6f73-132">Navigate to the project root.</span></span>
* <span data-ttu-id="e6f73-133">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e6f73-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="e6f73-134">键入默认提供程序的名称，或按 `Enter` 以使用默认 CDNJS 提供程序。</span><span class="sxs-lookup"><span data-stu-id="e6f73-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="e6f73-135">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="e6f73-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman 初始化命令 - 默认提供程序](_static/libman-init-provider.png)

<span data-ttu-id="e6f73-137">具有以下内容的 libman.json 文件会添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="e6f73-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="e6f73-138">添加库文件</span><span class="sxs-lookup"><span data-stu-id="e6f73-138">Add library files</span></span>

<span data-ttu-id="e6f73-139">`libman install` 命令会将库文件下载并安装到项目中。</span><span class="sxs-lookup"><span data-stu-id="e6f73-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="e6f73-140">如果 libman.json 文件不存在，则会添加一个。</span><span class="sxs-lookup"><span data-stu-id="e6f73-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="e6f73-141">libman.json 文件会进行修改，以存储库文件的配置详细信息。</span><span class="sxs-lookup"><span data-stu-id="e6f73-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e6f73-142">摘要</span><span class="sxs-lookup"><span data-stu-id="e6f73-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e6f73-143">自变量</span><span class="sxs-lookup"><span data-stu-id="e6f73-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e6f73-144">要安装的库的名称。</span><span class="sxs-lookup"><span data-stu-id="e6f73-144">The name of the library to install.</span></span> <span data-ttu-id="e6f73-145">此名称可以包含版本号表示法（例如 `@1.2.0`）。</span><span class="sxs-lookup"><span data-stu-id="e6f73-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="e6f73-146">选项</span><span class="sxs-lookup"><span data-stu-id="e6f73-146">Options</span></span>

<span data-ttu-id="e6f73-147">`libman install` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="e6f73-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="e6f73-148">用于安装库的位置。</span><span class="sxs-lookup"><span data-stu-id="e6f73-148">The location to install the library.</span></span> <span data-ttu-id="e6f73-149">如果未指定，则使用默认位置。</span><span class="sxs-lookup"><span data-stu-id="e6f73-149">If not specified, the default location is used.</span></span> <span data-ttu-id="e6f73-150">如果 libman.json 中未指定 `defaultDestination` 属性，则此选项是必需的。</span><span class="sxs-lookup"><span data-stu-id="e6f73-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="e6f73-151">指定要从库安装的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="e6f73-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="e6f73-152">如果未指定，则会安装库中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="e6f73-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="e6f73-153">为每个要安装的文件提供一个 `--files` 选项。</span><span class="sxs-lookup"><span data-stu-id="e6f73-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="e6f73-154">也支持相对路径。</span><span class="sxs-lookup"><span data-stu-id="e6f73-154">Relative paths are supported too.</span></span> <span data-ttu-id="e6f73-155">例如：`--files dist/browser/signalr.js`。</span><span class="sxs-lookup"><span data-stu-id="e6f73-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="e6f73-156">用于库获取的提供程序的名称。</span><span class="sxs-lookup"><span data-stu-id="e6f73-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="e6f73-157">将 `<PROVIDER>` 替换为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="e6f73-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="e6f73-158">如果未指定，则使用 libman.json 中的 `defaultProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="e6f73-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="e6f73-159">如果 libman.json 中未指定 `defaultProvider` 属性，则此选项是必需的。</span><span class="sxs-lookup"><span data-stu-id="e6f73-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e6f73-160">示例</span><span class="sxs-lookup"><span data-stu-id="e6f73-160">Examples</span></span>

<span data-ttu-id="e6f73-161">请考虑以下 libman.json 文件：</span><span class="sxs-lookup"><span data-stu-id="e6f73-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="e6f73-162">使用 CDNJS 提供程序将 jQuery 版本 3.2.1 jquery.min.js 文件安装到 wwwroot/scripts/jquery 文件夹：</span><span class="sxs-lookup"><span data-stu-id="e6f73-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="e6f73-163">libman.json 文件类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="e6f73-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="e6f73-164">使用文件系统提供程序从 C:\\temp\\contosoCalendar\\ 安装 calendar.js 和 calendar.css：</span><span class="sxs-lookup"><span data-stu-id="e6f73-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="e6f73-165">由于两个原因而出现以下提示：</span><span class="sxs-lookup"><span data-stu-id="e6f73-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="e6f73-166">libman.json 文件不包含 `defaultDestination` 属性。</span><span class="sxs-lookup"><span data-stu-id="e6f73-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="e6f73-167">`libman install` 命令不包含 `-d|--destination` 选项。</span><span class="sxs-lookup"><span data-stu-id="e6f73-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman 安装命令 - 目标](_static/libman-install-destination.png)

<span data-ttu-id="e6f73-169">接受默认目标之后，libman.json 文件类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="e6f73-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="e6f73-170">还原库文件</span><span class="sxs-lookup"><span data-stu-id="e6f73-170">Restore library files</span></span>

<span data-ttu-id="e6f73-171">`libman restore` 命令安装在 libman.json 中定义的库文件。</span><span class="sxs-lookup"><span data-stu-id="e6f73-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="e6f73-172">适用以下规则：</span><span class="sxs-lookup"><span data-stu-id="e6f73-172">The following rules apply:</span></span>

* <span data-ttu-id="e6f73-173">如果项目根中不存在 libman.json 文件，则会返回错误。</span><span class="sxs-lookup"><span data-stu-id="e6f73-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="e6f73-174">如果库指定了提供程序，则会忽略 libman.json 中的 `defaultProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="e6f73-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="e6f73-175">如果库指定了目标，则会忽略 libman.json 中的 `defaultDestination` 属性。</span><span class="sxs-lookup"><span data-stu-id="e6f73-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e6f73-176">摘要</span><span class="sxs-lookup"><span data-stu-id="e6f73-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e6f73-177">选项</span><span class="sxs-lookup"><span data-stu-id="e6f73-177">Options</span></span>

<span data-ttu-id="e6f73-178">`libman restore` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="e6f73-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e6f73-179">示例</span><span class="sxs-lookup"><span data-stu-id="e6f73-179">Examples</span></span>

<span data-ttu-id="e6f73-180">还原在 libman.json 中定义的库文件：</span><span class="sxs-lookup"><span data-stu-id="e6f73-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="e6f73-181">删除库文件</span><span class="sxs-lookup"><span data-stu-id="e6f73-181">Delete library files</span></span>

<span data-ttu-id="e6f73-182">`libman clean` 命令会删除之前通过 LibMan 还原的库文件。</span><span class="sxs-lookup"><span data-stu-id="e6f73-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="e6f73-183">在此操作之后成为空的文件夹会删除。</span><span class="sxs-lookup"><span data-stu-id="e6f73-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="e6f73-184">libman.json 的 `libraries` 属性中库文件的关联配置不会删除。</span><span class="sxs-lookup"><span data-stu-id="e6f73-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e6f73-185">摘要</span><span class="sxs-lookup"><span data-stu-id="e6f73-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e6f73-186">选项</span><span class="sxs-lookup"><span data-stu-id="e6f73-186">Options</span></span>

<span data-ttu-id="e6f73-187">`libman clean` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="e6f73-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e6f73-188">示例</span><span class="sxs-lookup"><span data-stu-id="e6f73-188">Examples</span></span>

<span data-ttu-id="e6f73-189">删除通过 LibMan 安装的库文件：</span><span class="sxs-lookup"><span data-stu-id="e6f73-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="e6f73-190">卸载库文件</span><span class="sxs-lookup"><span data-stu-id="e6f73-190">Uninstall library files</span></span>

<span data-ttu-id="e6f73-191">`libman uninstall` 命令：</span><span class="sxs-lookup"><span data-stu-id="e6f73-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="e6f73-192">从 libman.json 中的目标删除与指定库关联的所有文件。</span><span class="sxs-lookup"><span data-stu-id="e6f73-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="e6f73-193">从 libman.json 中删除关联库配置。</span><span class="sxs-lookup"><span data-stu-id="e6f73-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="e6f73-194">在下述情况中会发生错误：</span><span class="sxs-lookup"><span data-stu-id="e6f73-194">An error occurs when:</span></span>

* <span data-ttu-id="e6f73-195">项目根中不存在 libman.json 文件。</span><span class="sxs-lookup"><span data-stu-id="e6f73-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="e6f73-196">指定库不存在。</span><span class="sxs-lookup"><span data-stu-id="e6f73-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="e6f73-197">如果安装了多个具有相同名称的库，则系统会提示选择一个。</span><span class="sxs-lookup"><span data-stu-id="e6f73-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e6f73-198">摘要</span><span class="sxs-lookup"><span data-stu-id="e6f73-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e6f73-199">自变量</span><span class="sxs-lookup"><span data-stu-id="e6f73-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e6f73-200">要卸载的库的名称。</span><span class="sxs-lookup"><span data-stu-id="e6f73-200">The name of the library to uninstall.</span></span> <span data-ttu-id="e6f73-201">此名称可以包含版本号表示法（例如 `@1.2.0`）。</span><span class="sxs-lookup"><span data-stu-id="e6f73-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="e6f73-202">选项</span><span class="sxs-lookup"><span data-stu-id="e6f73-202">Options</span></span>

<span data-ttu-id="e6f73-203">`libman uninstall` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="e6f73-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e6f73-204">示例</span><span class="sxs-lookup"><span data-stu-id="e6f73-204">Examples</span></span>

<span data-ttu-id="e6f73-205">请考虑以下 libman.json 文件：</span><span class="sxs-lookup"><span data-stu-id="e6f73-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="e6f73-206">若要卸载 jQuery，以下任一命令都会成功：</span><span class="sxs-lookup"><span data-stu-id="e6f73-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="e6f73-207">卸载通过 `filesystem` 提供程序安装的 Lodash 文件：</span><span class="sxs-lookup"><span data-stu-id="e6f73-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="e6f73-208">更新库版本</span><span class="sxs-lookup"><span data-stu-id="e6f73-208">Update library version</span></span>

<span data-ttu-id="e6f73-209">`libman update` 命令会将通过 LibMan 安装的库更新为指定版本。</span><span class="sxs-lookup"><span data-stu-id="e6f73-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="e6f73-210">在下述情况中会发生错误：</span><span class="sxs-lookup"><span data-stu-id="e6f73-210">An error occurs when:</span></span>

* <span data-ttu-id="e6f73-211">项目根中不存在 libman.json 文件。</span><span class="sxs-lookup"><span data-stu-id="e6f73-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="e6f73-212">指定库不存在。</span><span class="sxs-lookup"><span data-stu-id="e6f73-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="e6f73-213">如果安装了多个具有相同名称的库，则系统会提示选择一个。</span><span class="sxs-lookup"><span data-stu-id="e6f73-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e6f73-214">摘要</span><span class="sxs-lookup"><span data-stu-id="e6f73-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e6f73-215">自变量</span><span class="sxs-lookup"><span data-stu-id="e6f73-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e6f73-216">要更新的库的名称。</span><span class="sxs-lookup"><span data-stu-id="e6f73-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="e6f73-217">选项</span><span class="sxs-lookup"><span data-stu-id="e6f73-217">Options</span></span>

<span data-ttu-id="e6f73-218">`libman update` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="e6f73-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="e6f73-219">获取库的最新预发行版本。</span><span class="sxs-lookup"><span data-stu-id="e6f73-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="e6f73-220">获取库的特定版本。</span><span class="sxs-lookup"><span data-stu-id="e6f73-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e6f73-221">示例</span><span class="sxs-lookup"><span data-stu-id="e6f73-221">Examples</span></span>

* <span data-ttu-id="e6f73-222">将 jQuery 更新为最新版本：</span><span class="sxs-lookup"><span data-stu-id="e6f73-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="e6f73-223">将 jQuery 更新为版本 3.3.1：</span><span class="sxs-lookup"><span data-stu-id="e6f73-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="e6f73-224">将 jQuery 更新为最新预发行版本：</span><span class="sxs-lookup"><span data-stu-id="e6f73-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="e6f73-225">管理库缓存</span><span class="sxs-lookup"><span data-stu-id="e6f73-225">Manage library cache</span></span>

<span data-ttu-id="e6f73-226">`libman cache` 命令会管理 LibMan 库缓存。</span><span class="sxs-lookup"><span data-stu-id="e6f73-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="e6f73-227">`filesystem` 提供程序不使用库缓存。</span><span class="sxs-lookup"><span data-stu-id="e6f73-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e6f73-228">摘要</span><span class="sxs-lookup"><span data-stu-id="e6f73-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e6f73-229">自变量</span><span class="sxs-lookup"><span data-stu-id="e6f73-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="e6f73-230">仅与 `clean` 命令一起使用。</span><span class="sxs-lookup"><span data-stu-id="e6f73-230">Only used with the `clean` command.</span></span> <span data-ttu-id="e6f73-231">指定要清除的提供程序缓存。</span><span class="sxs-lookup"><span data-stu-id="e6f73-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="e6f73-232">有效值包括：</span><span class="sxs-lookup"><span data-stu-id="e6f73-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="e6f73-233">选项</span><span class="sxs-lookup"><span data-stu-id="e6f73-233">Options</span></span>

<span data-ttu-id="e6f73-234">`libman cache` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="e6f73-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="e6f73-235">列出缓存的文件的名称。</span><span class="sxs-lookup"><span data-stu-id="e6f73-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="e6f73-236">列出缓存的库的名称。</span><span class="sxs-lookup"><span data-stu-id="e6f73-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e6f73-237">示例</span><span class="sxs-lookup"><span data-stu-id="e6f73-237">Examples</span></span>

* <span data-ttu-id="e6f73-238">若要查看每个提供程序的缓存库的名称，请使用以下命令之一：</span><span class="sxs-lookup"><span data-stu-id="e6f73-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="e6f73-239">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="e6f73-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="e6f73-240">查看每个提供程序的缓存库文件的名称：</span><span class="sxs-lookup"><span data-stu-id="e6f73-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="e6f73-241">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="e6f73-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="e6f73-242">请注意，前面的输出显示，jQuery 版本3.2.1 和3.3.1 缓存在 CDNJS 提供程序下。</span><span class="sxs-lookup"><span data-stu-id="e6f73-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="e6f73-243">清空 CDNJS 提供程序的库缓存：</span><span class="sxs-lookup"><span data-stu-id="e6f73-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="e6f73-244">清空 CDNJS 提供程序缓存之后，`libman cache list` 命令会显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="e6f73-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="e6f73-245">为所有支持的提供程序清空缓存：</span><span class="sxs-lookup"><span data-stu-id="e6f73-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="e6f73-246">清空所有提供程序缓存之后，`libman cache list` 命令会显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="e6f73-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="e6f73-247">其他资源</span><span class="sxs-lookup"><span data-stu-id="e6f73-247">Additional resources</span></span>

* [<span data-ttu-id="e6f73-248">安装全局工具</span><span class="sxs-lookup"><span data-stu-id="e6f73-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="e6f73-249">LibMan GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="e6f73-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
