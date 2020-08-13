---
title: 在 Visual Studio 中将 LibMan 与 ASP.NET Core 配合使用
author: scottaddie
description: 了解如何通过 Visual Studio 在 ASP.NET Core 项目中使用 LibMan。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: 2dc944ffd4307aa108a54b70d58f298c26959ce0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013341"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="2642f-103">在 Visual Studio 中将 LibMan 与 ASP.NET Core 配合使用</span><span class="sxs-lookup"><span data-stu-id="2642f-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="2642f-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="2642f-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="2642f-105">Visual Studio 对 ASP.NET Core 项目中的 [LibMan](xref:client-side/libman/index) 提供内置支持，包括：</span><span class="sxs-lookup"><span data-stu-id="2642f-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="2642f-106">支持在生成时配置和运行 LibMan 还原操作。</span><span class="sxs-lookup"><span data-stu-id="2642f-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="2642f-107">用于触发 LibMan 还原和清理操作的菜单项。</span><span class="sxs-lookup"><span data-stu-id="2642f-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="2642f-108">用于查找库并将文件添加到项目的搜索对话框。</span><span class="sxs-lookup"><span data-stu-id="2642f-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="2642f-109">编辑对 libman.json（LibMan 清单文件）的支持。</span><span class="sxs-lookup"><span data-stu-id="2642f-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="2642f-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2642f-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2642f-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="2642f-111">Prerequisites</span></span>

* <span data-ttu-id="2642f-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="2642f-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="2642f-113">添加库文件</span><span class="sxs-lookup"><span data-stu-id="2642f-113">Add library files</span></span>

<span data-ttu-id="2642f-114">可以通过两种不同的方式将库文件添加到 ASP.NET Core 项目中：</span><span class="sxs-lookup"><span data-stu-id="2642f-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="2642f-115">使用“添加客户端库”对话框</span><span class="sxs-lookup"><span data-stu-id="2642f-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="2642f-116">手动配置 LibMan 清单文件项</span><span class="sxs-lookup"><span data-stu-id="2642f-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="2642f-117">使用“添加客户端库”对话框</span><span class="sxs-lookup"><span data-stu-id="2642f-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="2642f-118">按照以下步骤安装客户端库：</span><span class="sxs-lookup"><span data-stu-id="2642f-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="2642f-119">在“解决方案资源管理器”中，右键单击要在其中添加文件的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="2642f-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="2642f-120">选择“添加” > “客户端库” 。</span><span class="sxs-lookup"><span data-stu-id="2642f-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="2642f-121">此时将显示“添加客户端库”对话框：</span><span class="sxs-lookup"><span data-stu-id="2642f-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![“添加客户端库”对话框](_static/add-library-dialog.png)

* <span data-ttu-id="2642f-123">从“提供程序”下拉列表中选择库提供程序。</span><span class="sxs-lookup"><span data-stu-id="2642f-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="2642f-124">CDNJS 是默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="2642f-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="2642f-125">在“库”文本框中键入要提取的库名称。</span><span class="sxs-lookup"><span data-stu-id="2642f-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="2642f-126">IntelliSense 提供以所提供文本开头的库的列表。</span><span class="sxs-lookup"><span data-stu-id="2642f-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="2642f-127">从 IntelliSense 列表中选择库。</span><span class="sxs-lookup"><span data-stu-id="2642f-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="2642f-128">请注意，库名称后缀带有 `@` 符号和所选提供程序的已知最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="2642f-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="2642f-129">确定要包含的文件：</span><span class="sxs-lookup"><span data-stu-id="2642f-129">Decide which files to include:</span></span>
  * <span data-ttu-id="2642f-130">选择“包含所有库文件”单选按钮以包含所有库文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="2642f-131">选择“选择特定文件”单选按钮以包含库文件的子集。</span><span class="sxs-lookup"><span data-stu-id="2642f-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="2642f-132">选中此单选按钮后，将启用文件选择器树。</span><span class="sxs-lookup"><span data-stu-id="2642f-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="2642f-133">选中要下载的文件名左侧的框。</span><span class="sxs-lookup"><span data-stu-id="2642f-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="2642f-134">在“目标位置”文本框中指定项目文件夹以存储文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="2642f-135">建议将每个库存储在单独的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2642f-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="2642f-136">建议的“目标位置”文件夹基于启动对话框的位置：</span><span class="sxs-lookup"><span data-stu-id="2642f-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="2642f-137">如果从项目根启动：</span><span class="sxs-lookup"><span data-stu-id="2642f-137">If launched from the project root:</span></span>
    * <span data-ttu-id="2642f-138">如果 wwwroot 存在，则使用 wwwroot/lib 。</span><span class="sxs-lookup"><span data-stu-id="2642f-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="2642f-139">如果 wwwroot 不存在，则使用 lib 。</span><span class="sxs-lookup"><span data-stu-id="2642f-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="2642f-140">如果从项目文件夹启动，则使用相应的文件夹名称。</span><span class="sxs-lookup"><span data-stu-id="2642f-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="2642f-141">文件夹建议带有库名称后缀。</span><span class="sxs-lookup"><span data-stu-id="2642f-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="2642f-142">下表说明了在 Razor Pages 项目中安装 jQuery 时的文件夹建议。</span><span class="sxs-lookup"><span data-stu-id="2642f-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="2642f-143">启动位置</span><span class="sxs-lookup"><span data-stu-id="2642f-143">Launch location</span></span>                           |<span data-ttu-id="2642f-144">建议的文件夹</span><span class="sxs-lookup"><span data-stu-id="2642f-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="2642f-145">项目根（如果 wwwroot 存在）</span><span class="sxs-lookup"><span data-stu-id="2642f-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="2642f-146">wwwroot/lib/jquery/</span><span class="sxs-lookup"><span data-stu-id="2642f-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="2642f-147">项目根（如果 wwwroot 不存在）</span><span class="sxs-lookup"><span data-stu-id="2642f-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="2642f-148">lib/jquery/</span><span class="sxs-lookup"><span data-stu-id="2642f-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="2642f-149">项目中的 Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="2642f-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="2642f-150">Pages/jquery/</span><span class="sxs-lookup"><span data-stu-id="2642f-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="2642f-151">单击“安装”按钮，根据 libman.json 中的配置下载文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="2642f-152">有关安装详细信息，请查看“输出”窗口的“库管理器”源 。</span><span class="sxs-lookup"><span data-stu-id="2642f-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="2642f-153">例如：</span><span class="sxs-lookup"><span data-stu-id="2642f-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="2642f-154">手动配置 LibMan 清单文件项</span><span class="sxs-lookup"><span data-stu-id="2642f-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="2642f-155">Visual Studio 中的所有 LibMan 操作都基于项目根的 LibMan 清单 (libman.json) 的内容。</span><span class="sxs-lookup"><span data-stu-id="2642f-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="2642f-156">可以手动编辑 libman.json，以配置项目的库文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="2642f-157">保存 libman.json 后，Visual Studio 将还原所有库文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="2642f-158">若要打开 libman.json 进行编辑，可以选择以下选项：</span><span class="sxs-lookup"><span data-stu-id="2642f-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="2642f-159">在“解决方案资源管理器”中双击 libman.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="2642f-160">右键单击“解决方案资源管理器”中的项目，然后选择“管理客户端库” 。</span><span class="sxs-lookup"><span data-stu-id="2642f-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="2642f-161">&#8224;</span><span class="sxs-lookup"><span data-stu-id="2642f-161">**&#8224;**</span></span>
* <span data-ttu-id="2642f-162">从 Visual Studio“项目”菜单中选择“管理客户端库” 。</span><span class="sxs-lookup"><span data-stu-id="2642f-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="2642f-163">&#8224;</span><span class="sxs-lookup"><span data-stu-id="2642f-163">**&#8224;**</span></span>

<span data-ttu-id="2642f-164">&#8224; 如果项目根中不存在 libman.json 文件，则将使用默认项模板内容创建该文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="2642f-165">Visual Studio 提供丰富的 JSON 编辑支持，如着色、格式设置、IntelliSense 和架构验证。</span><span class="sxs-lookup"><span data-stu-id="2642f-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="2642f-166">LibMan 清单的 JSON 架构位于 [https://json.schemastore.org/libman](https://json.schemastore.org/libman)。</span><span class="sxs-lookup"><span data-stu-id="2642f-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="2642f-167">对于下面的清单文件，LibMan 根据 `libraries` 属性中定义的配置检索文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="2642f-168">`libraries` 中定义的对象文字的说明如下所示：</span><span class="sxs-lookup"><span data-stu-id="2642f-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="2642f-169">从 CDNJS 提供程序检索 [jQuery](https://jquery.com/) 版本 3.3.1 的子集。</span><span class="sxs-lookup"><span data-stu-id="2642f-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="2642f-170">子集在 `files` 属性中定义&mdash;jquery node.js、jquery.js 和 jquery.min.map  。</span><span class="sxs-lookup"><span data-stu-id="2642f-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="2642f-171">文件位于项目的 wwwroot/lib/jquery 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2642f-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="2642f-172">检索整个[启动](https://getbootstrap.com/)版本 4.1.3，并放入 wwwroot/lib/bootstrap 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2642f-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="2642f-173">对象文字的 `provider` 属性将覆盖 `defaultProvider` 属性值。</span><span class="sxs-lookup"><span data-stu-id="2642f-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="2642f-174">LibMan 从 unpkg 提供程序检索启动文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="2642f-175">组织内的管理正文已批准 [Lodash](https://lodash.com/) 的子集。</span><span class="sxs-lookup"><span data-stu-id="2642f-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="2642f-176">从本地文件系统 C:\\temp\\lodash\\ 检索 lodash.js 和 lodash.min.js 文件  。</span><span class="sxs-lookup"><span data-stu-id="2642f-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="2642f-177">文件将复制到项目的 wwwroot/lib/lodash 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2642f-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="2642f-178">LibMan 仅支持每个提供程序的每个库的一个版本。</span><span class="sxs-lookup"><span data-stu-id="2642f-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="2642f-179">如果 libman.json 文件包含两个库，并且库名称与给定提供程序的库名称相同，则架构验证失败。</span><span class="sxs-lookup"><span data-stu-id="2642f-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="2642f-180">还原库文件</span><span class="sxs-lookup"><span data-stu-id="2642f-180">Restore library files</span></span>

<span data-ttu-id="2642f-181">若要从 Visual Studio 中还原库文件，项目根中必须有一个有效的 libman.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="2642f-182">还原的文件放置在项目中每个库指定的位置。</span><span class="sxs-lookup"><span data-stu-id="2642f-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="2642f-183">可以通过两种方式在 ASP.NET Core 项目中还原库文件：</span><span class="sxs-lookup"><span data-stu-id="2642f-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="2642f-184">在生成过程中还原文件</span><span class="sxs-lookup"><span data-stu-id="2642f-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="2642f-185">手动还原文件</span><span class="sxs-lookup"><span data-stu-id="2642f-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="2642f-186">在生成过程中还原文件</span><span class="sxs-lookup"><span data-stu-id="2642f-186">Restore files during build</span></span>

<span data-ttu-id="2642f-187">LibMan 可以在生成过程中还原定义的库文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="2642f-188">默认情况下，禁用 restore-on-build 行为。</span><span class="sxs-lookup"><span data-stu-id="2642f-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="2642f-189">启用和测试 restore-on-build 行为：</span><span class="sxs-lookup"><span data-stu-id="2642f-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="2642f-190">右键单击“解决方案资源管理器”中的 libman.json，然后从上下文菜单中选择“生成时启用还原客户端库” 。</span><span class="sxs-lookup"><span data-stu-id="2642f-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="2642f-191">系统提示安装 NuGet 包时，单击“是”按钮。</span><span class="sxs-lookup"><span data-stu-id="2642f-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="2642f-192">将 [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 包添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="2642f-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="2642f-193">生成项目以确认 LibMan 文件已还原。</span><span class="sxs-lookup"><span data-stu-id="2642f-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="2642f-194">`Microsoft.Web.LibraryManager.Build` 包将插入在项目的生成操作期间运行 LibMan 的 MSBuild 目标。</span><span class="sxs-lookup"><span data-stu-id="2642f-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="2642f-195">查看 LibMan 活动日志“输出”窗口的“生成”源 ：</span><span class="sxs-lookup"><span data-stu-id="2642f-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="2642f-196">当启用 restore-on-build 行为时，libman.json 上下文菜单将显示“生成时禁用还原客户端库”选项。</span><span class="sxs-lookup"><span data-stu-id="2642f-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="2642f-197">选择此选项将从项目文件中删除 `Microsoft.Web.LibraryManager.Build` 包引用。</span><span class="sxs-lookup"><span data-stu-id="2642f-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="2642f-198">因此，在每次生成时将不再还原客户端库。</span><span class="sxs-lookup"><span data-stu-id="2642f-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="2642f-199">无论 restore-on-build 设置如何，都可以随时从 libman.json 上下文菜单中手动进行还原。</span><span class="sxs-lookup"><span data-stu-id="2642f-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="2642f-200">有关详细信息，请参阅[还原文件](#restore-files-manually)。</span><span class="sxs-lookup"><span data-stu-id="2642f-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="2642f-201">手动还原文件</span><span class="sxs-lookup"><span data-stu-id="2642f-201">Restore files manually</span></span>

<span data-ttu-id="2642f-202">手动还原库文件：</span><span class="sxs-lookup"><span data-stu-id="2642f-202">To manually restore library files:</span></span>

* <span data-ttu-id="2642f-203">对于解决方案中的所有项目：</span><span class="sxs-lookup"><span data-stu-id="2642f-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="2642f-204">右键单击“解决方案资源管理器”中的解决方案名称。</span><span class="sxs-lookup"><span data-stu-id="2642f-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="2642f-205">选择“还原客户端库”选项。</span><span class="sxs-lookup"><span data-stu-id="2642f-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="2642f-206">对于特定项目：</span><span class="sxs-lookup"><span data-stu-id="2642f-206">For a specific project:</span></span>
  * <span data-ttu-id="2642f-207">右键单击“解决方案资源管理器”中的 libman.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="2642f-208">选择“还原客户端库”选项。</span><span class="sxs-lookup"><span data-stu-id="2642f-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="2642f-209">正在运行还原操作时：</span><span class="sxs-lookup"><span data-stu-id="2642f-209">While the restore operation is running:</span></span>

* <span data-ttu-id="2642f-210">将对 Visual Studio 状态栏上的任务状态中心 (TSC) 图标进行动画处理，并将显示“已启动还原操作”。</span><span class="sxs-lookup"><span data-stu-id="2642f-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="2642f-211">单击该图标将打开一个工具提示，其中列出了已知的后台任务。</span><span class="sxs-lookup"><span data-stu-id="2642f-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="2642f-212">消息将发送到状态栏和“输出”窗口的“库管理器”源 。</span><span class="sxs-lookup"><span data-stu-id="2642f-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="2642f-213">例如：</span><span class="sxs-lookup"><span data-stu-id="2642f-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="2642f-214">删除库文件</span><span class="sxs-lookup"><span data-stu-id="2642f-214">Delete library files</span></span>

<span data-ttu-id="2642f-215">执行清理操作，该操作将删除之前在 Visual Studio 中还原的库文件：</span><span class="sxs-lookup"><span data-stu-id="2642f-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="2642f-216">右键单击“解决方案资源管理器”中的 libman.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="2642f-217">选择“清理客户端库”选项。</span><span class="sxs-lookup"><span data-stu-id="2642f-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="2642f-218">为防止意外删除非库文件，清理操作不会删除整个目录。</span><span class="sxs-lookup"><span data-stu-id="2642f-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="2642f-219">它仅删除先前还原中包含的文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="2642f-220">正在运行清理操作时：</span><span class="sxs-lookup"><span data-stu-id="2642f-220">While the clean operation is running:</span></span>

* <span data-ttu-id="2642f-221">将对 Visual Studio 状态栏上的 TSC 图标进行动画处理，并将显示“已启动客户端库操作”。</span><span class="sxs-lookup"><span data-stu-id="2642f-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="2642f-222">单击该图标将打开一个工具提示，其中列出了已知的后台任务。</span><span class="sxs-lookup"><span data-stu-id="2642f-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="2642f-223">将消息发送到状态栏和“输出”窗口的“库管理器”源 。</span><span class="sxs-lookup"><span data-stu-id="2642f-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="2642f-224">例如：</span><span class="sxs-lookup"><span data-stu-id="2642f-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="2642f-225">清理操作仅从项目中删除文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="2642f-226">库文件保留在缓存中，以便在将来的还原操作中更快地检索。</span><span class="sxs-lookup"><span data-stu-id="2642f-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="2642f-227">若要管理存储在本地计算机缓存中的库文件，请使用 [LibMan CLI](xref:client-side/libman/libman-cli)。</span><span class="sxs-lookup"><span data-stu-id="2642f-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="2642f-228">卸载库文件</span><span class="sxs-lookup"><span data-stu-id="2642f-228">Uninstall library files</span></span>

<span data-ttu-id="2642f-229">卸载库文件：</span><span class="sxs-lookup"><span data-stu-id="2642f-229">To uninstall library files:</span></span>

* <span data-ttu-id="2642f-230">打开 libman.json。</span><span class="sxs-lookup"><span data-stu-id="2642f-230">Open *libman.json*.</span></span>
* <span data-ttu-id="2642f-231">将插入符号置于相应的 `libraries` 对象文字内。</span><span class="sxs-lookup"><span data-stu-id="2642f-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="2642f-232">单击左边距中显示的灯泡图标，然后选择“卸载 \<library_name>@\<library_version>”：</span><span class="sxs-lookup"><span data-stu-id="2642f-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![卸载库上下文菜单选项](_static/uninstall-menu-option.png)

<span data-ttu-id="2642f-234">或者可以手动编辑并保存 LibMan 清单 (libman.json)。</span><span class="sxs-lookup"><span data-stu-id="2642f-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="2642f-235">保存文件后将运行[还原操作](#restore-library-files)。</span><span class="sxs-lookup"><span data-stu-id="2642f-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="2642f-236">libman.json 中不再定义的库文件将从项目中删除。</span><span class="sxs-lookup"><span data-stu-id="2642f-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="2642f-237">更新库版本</span><span class="sxs-lookup"><span data-stu-id="2642f-237">Update library version</span></span>

<span data-ttu-id="2642f-238">检查更新的库版本：</span><span class="sxs-lookup"><span data-stu-id="2642f-238">To check for an updated library version:</span></span>

* <span data-ttu-id="2642f-239">打开 libman.json。</span><span class="sxs-lookup"><span data-stu-id="2642f-239">Open *libman.json*.</span></span>
* <span data-ttu-id="2642f-240">将插入符号置于相应的 `libraries` 对象文字内。</span><span class="sxs-lookup"><span data-stu-id="2642f-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="2642f-241">单击左边距中显示的灯泡图标。</span><span class="sxs-lookup"><span data-stu-id="2642f-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="2642f-242">将鼠标悬停在“检查更新”上。</span><span class="sxs-lookup"><span data-stu-id="2642f-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="2642f-243">LibMan 检查库版本是否比安装的版本更新。</span><span class="sxs-lookup"><span data-stu-id="2642f-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="2642f-244">可能出现以下结果：</span><span class="sxs-lookup"><span data-stu-id="2642f-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="2642f-245">如果已安装最新版本，则不会显示“找不到更新”消息。</span><span class="sxs-lookup"><span data-stu-id="2642f-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="2642f-246">如果尚未安装，则会显示最新的稳定版本。</span><span class="sxs-lookup"><span data-stu-id="2642f-246">The latest stable version is displayed if not already installed.</span></span>

  ![检查更新上下文菜单选项](_static/update-menu-option.png)

* <span data-ttu-id="2642f-248">如果有比已安装版本更新的预发行版可用，则会显示该预发行版。</span><span class="sxs-lookup"><span data-stu-id="2642f-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="2642f-249">若要降级到较旧的库版本，请手动编辑 libman.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="2642f-250">保存文件后，LibMan [还原操作](#restore-library-files)将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2642f-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="2642f-251">删除先前版本中的冗余文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="2642f-252">从新版本添加新文件和更新文件。</span><span class="sxs-lookup"><span data-stu-id="2642f-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2642f-253">其他资源</span><span class="sxs-lookup"><span data-stu-id="2642f-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="2642f-254">LibMan GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="2642f-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
