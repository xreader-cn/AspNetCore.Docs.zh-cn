---
title: 在 Visual Studio 中的 ASP.NET Core 中使用 LibMan
author: scottaddie
description: 了解如何使用 Visual Studio 的 ASP.NET Core 项目中使用 LibMan。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: 41a5a41c8921b04290784d26441ecb46aea753e7
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894964"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="6a01d-103">在 Visual Studio 中的 ASP.NET Core 中使用 LibMan</span><span class="sxs-lookup"><span data-stu-id="6a01d-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="6a01d-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6a01d-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6a01d-105">Visual Studio 在 ASP.NET Core 项目中内置了对 [LibMan](xref:client-side/libman/index) 的支持，包括：</span><span class="sxs-lookup"><span data-stu-id="6a01d-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="6a01d-106">支持在生成时配置和运行 LibMan 还原操作。</span><span class="sxs-lookup"><span data-stu-id="6a01d-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="6a01d-107">用于触发 LibMan 还原和清理操作的菜单项。</span><span class="sxs-lookup"><span data-stu-id="6a01d-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="6a01d-108">搜索对话框，查找库并将文件添加到项目中。</span><span class="sxs-lookup"><span data-stu-id="6a01d-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="6a01d-109">编辑对*libman.json*（LibMan  清单文件）的支持。&mdash;</span><span class="sxs-lookup"><span data-stu-id="6a01d-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="6a01d-110">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6a01d-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a01d-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="6a01d-111">Prerequisites</span></span>

* <span data-ttu-id="6a01d-112">Visual Studio 2017 版本 15.8 或更高版本，且具有**ASP.NET 和 Web 开发**工作负载</span><span class="sxs-lookup"><span data-stu-id="6a01d-112">Visual Studio 2017 version 15.8 or later with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="6a01d-113">添加库文件</span><span class="sxs-lookup"><span data-stu-id="6a01d-113">Add library files</span></span>

<span data-ttu-id="6a01d-114">可通过两种不同的方式将库文件添加到 ASP.NET Core 项目中：</span><span class="sxs-lookup"><span data-stu-id="6a01d-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="6a01d-115">使用“添加客户端库”对话框</span><span class="sxs-lookup"><span data-stu-id="6a01d-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="6a01d-116">手动配置 LibMan 清单文件条目</span><span class="sxs-lookup"><span data-stu-id="6a01d-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="6a01d-117">使用“添加客户端库”对话框</span><span class="sxs-lookup"><span data-stu-id="6a01d-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="6a01d-118">请按照下列步骤以安装客户端库：</span><span class="sxs-lookup"><span data-stu-id="6a01d-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="6a01d-119">在中**解决方案资源管理器**，右键单击项目文件夹应在其中添加文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="6a01d-120">选择**添加** > **客户端库**。</span><span class="sxs-lookup"><span data-stu-id="6a01d-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="6a01d-121">**添加客户端库**此时将显示对话框：</span><span class="sxs-lookup"><span data-stu-id="6a01d-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![添加客户端库对话框](_static/add-library-dialog.png)

* <span data-ttu-id="6a01d-123">选择从库提供程序**提供程序**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="6a01d-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="6a01d-124">CDNJS 是默认提供程序。</span><span class="sxs-lookup"><span data-stu-id="6a01d-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="6a01d-125">类型库名称中提取**库**文本框。</span><span class="sxs-lookup"><span data-stu-id="6a01d-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="6a01d-126">IntelliSense 提供了一系列从提供的文本的库。</span><span class="sxs-lookup"><span data-stu-id="6a01d-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="6a01d-127">从智能感知列表中选择的库。</span><span class="sxs-lookup"><span data-stu-id="6a01d-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="6a01d-128">请注意，库名称作为后缀`@`符号和已知的所选提供程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="6a01d-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="6a01d-129">确定要包括的文件：</span><span class="sxs-lookup"><span data-stu-id="6a01d-129">Decide which files to include:</span></span>
  * <span data-ttu-id="6a01d-130">选择**包括所有库文件**单选按钮，以都包括所有库的文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="6a01d-131">选择**选择特定的文件**单选按钮，以包含库的文件的子集。</span><span class="sxs-lookup"><span data-stu-id="6a01d-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="6a01d-132">选中的单选按钮后，启用文件选择器树。</span><span class="sxs-lookup"><span data-stu-id="6a01d-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="6a01d-133">检查要下载的文件名称左侧的框。</span><span class="sxs-lookup"><span data-stu-id="6a01d-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="6a01d-134">指定用于存储中的文件的项目文件夹**目标位置**文本框。</span><span class="sxs-lookup"><span data-stu-id="6a01d-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="6a01d-135">为建议，将每个库存储在单独的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a01d-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="6a01d-136">建议**目标位置**文件夹根据从中启动对话框的位置：</span><span class="sxs-lookup"><span data-stu-id="6a01d-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="6a01d-137">如果从项目根目录中启动：</span><span class="sxs-lookup"><span data-stu-id="6a01d-137">If launched from the project root:</span></span>
    * <span data-ttu-id="6a01d-138">*wwwroot/lib*如果，则使用*wwwroot*存在。</span><span class="sxs-lookup"><span data-stu-id="6a01d-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="6a01d-139">*lib*如果，则使用*wwwroot*不存在。</span><span class="sxs-lookup"><span data-stu-id="6a01d-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="6a01d-140">如果从项目文件夹启动，则使用相应的文件夹名称。</span><span class="sxs-lookup"><span data-stu-id="6a01d-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="6a01d-141">库名称作为后缀文件夹建议。</span><span class="sxs-lookup"><span data-stu-id="6a01d-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="6a01d-142">Razor 页面项目中安装 jQuery 时下, 表说明了文件夹的建议。</span><span class="sxs-lookup"><span data-stu-id="6a01d-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="6a01d-143">启动位置</span><span class="sxs-lookup"><span data-stu-id="6a01d-143">Launch location</span></span>                           |<span data-ttu-id="6a01d-144">建议的文件夹</span><span class="sxs-lookup"><span data-stu-id="6a01d-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="6a01d-145">项目根目录 (如果*wwwroot*存在)</span><span class="sxs-lookup"><span data-stu-id="6a01d-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="6a01d-146">*wwwroot/lib/jquery /*</span><span class="sxs-lookup"><span data-stu-id="6a01d-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="6a01d-147">项目根目录 (如果*wwwroot*不存在)</span><span class="sxs-lookup"><span data-stu-id="6a01d-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="6a01d-148">*lib/jquery /*</span><span class="sxs-lookup"><span data-stu-id="6a01d-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="6a01d-149">*页*项目文件夹中</span><span class="sxs-lookup"><span data-stu-id="6a01d-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="6a01d-150">*页/jquery /*</span><span class="sxs-lookup"><span data-stu-id="6a01d-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="6a01d-151">单击**安装**按钮以下载的文件，每个中配置*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="6a01d-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="6a01d-152">审阅**库管理器**的源**输出**安装详细信息窗口。</span><span class="sxs-lookup"><span data-stu-id="6a01d-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="6a01d-153">例如：</span><span class="sxs-lookup"><span data-stu-id="6a01d-153">For example:</span></span>

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

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="6a01d-154">手动配置 LibMan 清单文件条目</span><span class="sxs-lookup"><span data-stu-id="6a01d-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="6a01d-155">在 Visual Studio 中的所有 LibMan 操作都基于项目根 LibMan 清单的内容 (*libman.json*)。</span><span class="sxs-lookup"><span data-stu-id="6a01d-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="6a01d-156">您可以手动编辑*libman.json*若要配置的项目的库文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="6a01d-157">Visual Studio 还原所有库文件一次*libman.json*保存。</span><span class="sxs-lookup"><span data-stu-id="6a01d-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="6a01d-158">若要打开*libman.json*进行编辑，存在以下选项：</span><span class="sxs-lookup"><span data-stu-id="6a01d-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="6a01d-159">双击*libman.json*中的文件**解决方案资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="6a01d-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="6a01d-160">右键单击该项目中的**解决方案资源管理器**，然后选择**管理客户端库**。</span><span class="sxs-lookup"><span data-stu-id="6a01d-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="6a01d-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="6a01d-161">**&#8224;**</span></span>
* <span data-ttu-id="6a01d-162">选择**管理客户端库**从 Visual Studio**项目**菜单。</span><span class="sxs-lookup"><span data-stu-id="6a01d-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="6a01d-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="6a01d-163">**&#8224;**</span></span>

<span data-ttu-id="6a01d-164">**&#8224;** 如果*libman.json*文件尚不存在项目根目录中，它将使用默认项目模板内容的创建。</span><span class="sxs-lookup"><span data-stu-id="6a01d-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="6a01d-165">Visual Studio 提供了丰富 JSON 编辑支持，例如颜色设置、 格式设置、 IntelliSense 和架构验证。</span><span class="sxs-lookup"><span data-stu-id="6a01d-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="6a01d-166">在找到 LibMan 清单的 JSON 架构[ http://json.schemastore.org/libman ](http://json.schemastore.org/libman)。</span><span class="sxs-lookup"><span data-stu-id="6a01d-166">The LibMan manifest's JSON schema is found at [http://json.schemastore.org/libman](http://json.schemastore.org/libman).</span></span>

<span data-ttu-id="6a01d-167">使用以下清单文件，LibMan 检索每中定义的配置文件`libraries`属性。</span><span class="sxs-lookup"><span data-stu-id="6a01d-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="6a01d-168">中定义的对象文字说明`libraries`后面：</span><span class="sxs-lookup"><span data-stu-id="6a01d-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="6a01d-169">一个子集[jQuery](https://jquery.com/)版本 3.3.1 检索从 CDNJS 提供程序。</span><span class="sxs-lookup"><span data-stu-id="6a01d-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="6a01d-170">在中定义的子集`files`属性&mdash;*jquery.min.js*，*只需要 jquery.js*，以及*jquery.min.map*。</span><span class="sxs-lookup"><span data-stu-id="6a01d-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="6a01d-171">将文件放置在项目的*wwwroot/lib/jquery*文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a01d-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="6a01d-172">整个[Bootstrap](https://getbootstrap.com/)检索并放入版本 4.1.3 *wwwroot/lib/bootstrap*文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a01d-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="6a01d-173">对象文字`provider`属性重写`defaultProvider`属性值。</span><span class="sxs-lookup"><span data-stu-id="6a01d-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="6a01d-174">LibMan 从 unpkg 提供程序中检索启动文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="6a01d-175">一个子集[lodash 等](https://lodash.com/)监管主体在组织内已批准。</span><span class="sxs-lookup"><span data-stu-id="6a01d-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="6a01d-176">*Lodash.js*并*lodash.min.js*从本地文件系统中检索文件*c:\\temp\\lodash 等\\*。</span><span class="sxs-lookup"><span data-stu-id="6a01d-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="6a01d-177">将文件复制到项目的*lodash 等wwwroot/lib/* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a01d-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="6a01d-178">LibMan 仅支持每个提供程序从每个库的一个版本。</span><span class="sxs-lookup"><span data-stu-id="6a01d-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="6a01d-179">*Libman.json*文件未通过架构验证，如果它包含与给定的提供程序库同名的两个库。</span><span class="sxs-lookup"><span data-stu-id="6a01d-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="6a01d-180">库将文件还原</span><span class="sxs-lookup"><span data-stu-id="6a01d-180">Restore library files</span></span>

<span data-ttu-id="6a01d-181">若要还原从 Visual Studio 中的库文件，必须有一个有效*libman.json*项目根目录中的文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="6a01d-182">还原的文件放置在项目中指定每个库的位置。</span><span class="sxs-lookup"><span data-stu-id="6a01d-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="6a01d-183">可以通过两种方式的 ASP.NET Core 项目中还原库文件：</span><span class="sxs-lookup"><span data-stu-id="6a01d-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="6a01d-184">在生成过程中还原文件</span><span class="sxs-lookup"><span data-stu-id="6a01d-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="6a01d-185">手动还原文件</span><span class="sxs-lookup"><span data-stu-id="6a01d-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="6a01d-186">在生成过程中还原文件</span><span class="sxs-lookup"><span data-stu-id="6a01d-186">Restore files during build</span></span>

<span data-ttu-id="6a01d-187">LibMan 可以作为生成过程的一部分还原的已定义的库文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="6a01d-188">默认情况下*还原上生成*禁用行为。</span><span class="sxs-lookup"><span data-stu-id="6a01d-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="6a01d-189">若要启用和测试还原上生成行为：</span><span class="sxs-lookup"><span data-stu-id="6a01d-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="6a01d-190">右键单击*libman.json*中**解决方案资源管理器**，然后选择**启用还原客户端上的库生成**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="6a01d-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="6a01d-191">单击**是**按钮当系统提示安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="6a01d-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="6a01d-192">[Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 包添加到项目：</span><span class="sxs-lookup"><span data-stu-id="6a01d-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="6a01d-193">生成项目，以确认 LibMan 文件还原发生。</span><span class="sxs-lookup"><span data-stu-id="6a01d-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="6a01d-194">`Microsoft.Web.LibraryManager.Build`包注入项目的生成操作期间运行 LibMan 的 MSBuild 目标。</span><span class="sxs-lookup"><span data-stu-id="6a01d-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="6a01d-195">审阅**构建**的源**输出**LibMan 活动日志窗口：</span><span class="sxs-lookup"><span data-stu-id="6a01d-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

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

<span data-ttu-id="6a01d-196">启用还原上生成行为后， *libman.json*上下文菜单将显示**禁用还原客户端上的库生成**选项。</span><span class="sxs-lookup"><span data-stu-id="6a01d-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="6a01d-197">选择此选项删除`Microsoft.Web.LibraryManager.Build`包从项目文件的引用。</span><span class="sxs-lookup"><span data-stu-id="6a01d-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="6a01d-198">因此，客户端库不再存储在每个版本。</span><span class="sxs-lookup"><span data-stu-id="6a01d-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="6a01d-199">而不考虑还原上生成设置中，您可以手动还原在从任何时候*libman.json*上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="6a01d-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="6a01d-200">有关详细信息，请参阅[手动还原文件](#restore-files-manually)。</span><span class="sxs-lookup"><span data-stu-id="6a01d-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="6a01d-201">手动还原文件</span><span class="sxs-lookup"><span data-stu-id="6a01d-201">Restore files manually</span></span>

<span data-ttu-id="6a01d-202">若要手动还原库文件：</span><span class="sxs-lookup"><span data-stu-id="6a01d-202">To manually restore library files:</span></span>

* <span data-ttu-id="6a01d-203">对于解决方案中的所有项目：</span><span class="sxs-lookup"><span data-stu-id="6a01d-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="6a01d-204">右键单击解决方案的名称**解决方案资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="6a01d-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="6a01d-205">选择**还原客户端库**选项。</span><span class="sxs-lookup"><span data-stu-id="6a01d-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="6a01d-206">对于特定的项目：</span><span class="sxs-lookup"><span data-stu-id="6a01d-206">For a specific project:</span></span>
  * <span data-ttu-id="6a01d-207">右键单击*libman.json*中的文件**解决方案资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="6a01d-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="6a01d-208">选择**还原客户端库**选项。</span><span class="sxs-lookup"><span data-stu-id="6a01d-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="6a01d-209">尽管还原操作正在运行：</span><span class="sxs-lookup"><span data-stu-id="6a01d-209">While the restore operation is running:</span></span>

* <span data-ttu-id="6a01d-210">Visual Studio 在状态栏上的任务状态中心 (TSC) 图标进行动画处理，并将读取*还原操作已启动*。</span><span class="sxs-lookup"><span data-stu-id="6a01d-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="6a01d-211">单击该图标可打开列出已知的后台任务的工具提示。</span><span class="sxs-lookup"><span data-stu-id="6a01d-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="6a01d-212">消息将发送到状态栏并**库管理器**的源**输出**窗口。</span><span class="sxs-lookup"><span data-stu-id="6a01d-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="6a01d-213">例如：</span><span class="sxs-lookup"><span data-stu-id="6a01d-213">For example:</span></span>

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

## <a name="delete-library-files"></a><span data-ttu-id="6a01d-214">删除库文件</span><span class="sxs-lookup"><span data-stu-id="6a01d-214">Delete library files</span></span>

<span data-ttu-id="6a01d-215">若要执行*干净*操作，这将删除以前还原在 Visual Studio 中的库文件：</span><span class="sxs-lookup"><span data-stu-id="6a01d-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="6a01d-216">右键单击*libman.json*中的文件**解决方案资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="6a01d-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="6a01d-217">选择**干净的客户端库**选项。</span><span class="sxs-lookup"><span data-stu-id="6a01d-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="6a01d-218">若要防止无意删除非库文件，清除操作不会删除整个目录。</span><span class="sxs-lookup"><span data-stu-id="6a01d-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="6a01d-219">而只会删除包括在上一个还原的文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="6a01d-220">尽管运行清理操作：</span><span class="sxs-lookup"><span data-stu-id="6a01d-220">While the clean operation is running:</span></span>

* <span data-ttu-id="6a01d-221">Visual Studio 在状态栏上的 TSC 图标进行动画处理，并将读取*启动客户端库操作*。</span><span class="sxs-lookup"><span data-stu-id="6a01d-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="6a01d-222">单击该图标可打开列出已知的后台任务的工具提示。</span><span class="sxs-lookup"><span data-stu-id="6a01d-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="6a01d-223">消息发送到状态栏并**库管理器**的源**输出**窗口。</span><span class="sxs-lookup"><span data-stu-id="6a01d-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="6a01d-224">例如：</span><span class="sxs-lookup"><span data-stu-id="6a01d-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="6a01d-225">清理操作只会从项目删除文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="6a01d-226">在将来还原操作的更快地检索缓存中保留库文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="6a01d-227">若要管理存储在本地计算机的缓存中的库文件，请使用[LibMan CLI](xref:client-side/libman/libman-cli)。</span><span class="sxs-lookup"><span data-stu-id="6a01d-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="6a01d-228">卸载库文件</span><span class="sxs-lookup"><span data-stu-id="6a01d-228">Uninstall library files</span></span>

<span data-ttu-id="6a01d-229">若要卸载库文件：</span><span class="sxs-lookup"><span data-stu-id="6a01d-229">To uninstall library files:</span></span>

* <span data-ttu-id="6a01d-230">打开*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="6a01d-230">Open *libman.json*.</span></span>
* <span data-ttu-id="6a01d-231">定位插入符号置于相应`libraries`对象文字。</span><span class="sxs-lookup"><span data-stu-id="6a01d-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="6a01d-232">单击左边距中显示的灯泡图标，然后选择**卸载\<library_name > @\<library_version >**:</span><span class="sxs-lookup"><span data-stu-id="6a01d-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![卸载库上下文菜单选项](_static/uninstall-menu-option.png)

<span data-ttu-id="6a01d-234">或者，可以手动编辑和保存 LibMan 清单 (*libman.json*)。</span><span class="sxs-lookup"><span data-stu-id="6a01d-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="6a01d-235">[还原操作](#restore-library-files)运行时保存该文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="6a01d-236">不能再中定义的库文件*libman.json*从项目中删除。</span><span class="sxs-lookup"><span data-stu-id="6a01d-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="6a01d-237">更新库版本</span><span class="sxs-lookup"><span data-stu-id="6a01d-237">Update library version</span></span>

<span data-ttu-id="6a01d-238">若要检查有更新的库版本：</span><span class="sxs-lookup"><span data-stu-id="6a01d-238">To check for an updated library version:</span></span>

* <span data-ttu-id="6a01d-239">打开*libman.json*。</span><span class="sxs-lookup"><span data-stu-id="6a01d-239">Open *libman.json*.</span></span>
* <span data-ttu-id="6a01d-240">定位插入符号置于相应`libraries`对象文字。</span><span class="sxs-lookup"><span data-stu-id="6a01d-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="6a01d-241">单击灯泡图标显示在左边距中。</span><span class="sxs-lookup"><span data-stu-id="6a01d-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="6a01d-242">将鼠标悬停**检查是否有更新**。</span><span class="sxs-lookup"><span data-stu-id="6a01d-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="6a01d-243">LibMan 检查安装的版本比新的库版本。</span><span class="sxs-lookup"><span data-stu-id="6a01d-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="6a01d-244">会出现以下结果：</span><span class="sxs-lookup"><span data-stu-id="6a01d-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="6a01d-245">一个**找不到更新**如果已安装最新版本，则会显示消息。</span><span class="sxs-lookup"><span data-stu-id="6a01d-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="6a01d-246">最新稳定版本是显示如果还没有安装。</span><span class="sxs-lookup"><span data-stu-id="6a01d-246">The latest stable version is displayed if not already installed.</span></span>

  ![检查更新上下文菜单选项](_static/update-menu-option.png)

* <span data-ttu-id="6a01d-248">如果现已安装的版本比新预发布版本，会显示预发行版。</span><span class="sxs-lookup"><span data-stu-id="6a01d-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="6a01d-249">若要降级到较旧的库版本，手动编辑*libman.json*文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="6a01d-250">保存文件时，LibMan[还原操作](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="6a01d-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="6a01d-251">从以前的版本中移除冗余的文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="6a01d-252">新版本中添加新的和更新文件。</span><span class="sxs-lookup"><span data-stu-id="6a01d-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a01d-253">其他资源</span><span class="sxs-lookup"><span data-stu-id="6a01d-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="6a01d-254">LibMan GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="6a01d-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
