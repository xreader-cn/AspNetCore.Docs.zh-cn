---
title: 管理 ASP.NET Core 中的 Bower 的客户端包
author: rick-anderson
description: 使用 Bower 管理客户端的包。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 1292b75bdfe1cea56d9c8bc282b0dd90fc945a33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813534"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a><span data-ttu-id="4e910-103">管理 ASP.NET Core 中的 Bower 的客户端包</span><span class="sxs-lookup"><span data-stu-id="4e910-103">Manage client-side packages with Bower in ASP.NET Core</span></span>

<span data-ttu-id="4e910-104">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Noel 大米](https://twitter.com/noelrice1)，和[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="4e910-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel Rice](https://twitter.com/noelrice1), and [Scott Addie](https://scottaddie.com)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e910-105">而维护 Bower，其维护人员建议使用另一种解决方案。</span><span class="sxs-lookup"><span data-stu-id="4e910-105">While Bower is maintained, its maintainers recommend using a different solution.</span></span> <span data-ttu-id="4e910-106">[库管理器](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/)(简称 LibMan) 是 Visual Studio 的新客户端库获取工具 (Visual Studio 15.8 或更高版本)。</span><span class="sxs-lookup"><span data-stu-id="4e910-106">[Library Manager](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan for short) is Visual Studio's new client-side library acquisition tool (Visual Studio 15.8 or later).</span></span> <span data-ttu-id="4e910-107">有关详细信息，请参阅 <xref:client-side/libman/index>。</span><span class="sxs-lookup"><span data-stu-id="4e910-107">For more information, see <xref:client-side/libman/index>.</span></span> <span data-ttu-id="4e910-108">Bower 在 Visual Studio 中受支持版本 15.5 通过。</span><span class="sxs-lookup"><span data-stu-id="4e910-108">Bower is supported in Visual Studio through version 15.5.</span></span>
>
> <span data-ttu-id="4e910-109">Yarn Webpack 使用是的其中一个受欢迎的替代方法[迁移说明](https://bower.io/blog/2017/how-to-migrate-away-from-bower/)可用。</span><span class="sxs-lookup"><span data-stu-id="4e910-109">Yarn with Webpack is one popular alternative for which [migration instructions](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) are available.</span></span>

<span data-ttu-id="4e910-110">[Bower](https://bower.io/)调用其自身"web 包管理器"。</span><span class="sxs-lookup"><span data-stu-id="4e910-110">[Bower](https://bower.io/) calls itself "A package manager for the web".</span></span> <span data-ttu-id="4e910-111">在.NET 生态系统，它填补留下的 NuGet 的无法传送静态内容的文件。</span><span class="sxs-lookup"><span data-stu-id="4e910-111">Within the .NET ecosystem, it fills the void left by NuGet's inability to deliver static content files.</span></span> <span data-ttu-id="4e910-112">对于 ASP.NET Core 项目，这些静态文件，则所固有的客户端库，如[jQuery](https://jquery.com/)和[Bootstrap](https://getbootstrap.com/)。</span><span class="sxs-lookup"><span data-stu-id="4e910-112">For ASP.NET Core projects, these static files are inherent to client-side libraries like [jQuery](https://jquery.com/) and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="4e910-113">对于.NET 库，您仍使用[NuGet](https://www.nuget.org/)包管理器。</span><span class="sxs-lookup"><span data-stu-id="4e910-113">For .NET libraries, you still use [NuGet](https://www.nuget.org/) package manager.</span></span>

<span data-ttu-id="4e910-114">设置客户端的 ASP.NET Core 项目模板创建的新项目生成过程。</span><span class="sxs-lookup"><span data-stu-id="4e910-114">New projects created with the ASP.NET Core project templates set up the client-side build process.</span></span> <span data-ttu-id="4e910-115">[jQuery](https://jquery.com/)并[Bootstrap](https://getbootstrap.com/)安装，并支持 Bower。</span><span class="sxs-lookup"><span data-stu-id="4e910-115">[jQuery](https://jquery.com/) and [Bootstrap](https://getbootstrap.com/) are installed, and Bower is supported.</span></span>

<span data-ttu-id="4e910-116">客户端的包中列出*bower.json*文件。</span><span class="sxs-lookup"><span data-stu-id="4e910-116">Client-side packages are listed in the *bower.json* file.</span></span> <span data-ttu-id="4e910-117">ASP.NET Core 项目模板配置*bower.json* jQuery、 jQuery 验证与 Bootstrap。</span><span class="sxs-lookup"><span data-stu-id="4e910-117">The ASP.NET Core project templates configures *bower.json* with jQuery, jQuery validation, and Bootstrap.</span></span>

<span data-ttu-id="4e910-118">在本教程中，我们将添加对的支持[Font Awesome](http://fontawesome.io)。</span><span class="sxs-lookup"><span data-stu-id="4e910-118">In this tutorial, we'll add support for [Font Awesome](http://fontawesome.io).</span></span> <span data-ttu-id="4e910-119">可以使用安装 bower 程序包**管理 Bower 程序包**UI 或手动*bower.json*文件。</span><span class="sxs-lookup"><span data-stu-id="4e910-119">Bower packages can be installed with the **Manage Bower Packages** UI or manually in the *bower.json* file.</span></span>

### <a name="installation-via-manage-bower-packages-ui"></a><span data-ttu-id="4e910-120">通过管理 Bower 程序包 UI 安装</span><span class="sxs-lookup"><span data-stu-id="4e910-120">Installation via Manage Bower Packages UI</span></span>

* <span data-ttu-id="4e910-121">创建新的 ASP.NET Core Web 应用程序与**ASP.NET Core Web 应用程序 (.NET Core)** 模板。</span><span class="sxs-lookup"><span data-stu-id="4e910-121">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="4e910-122">选择**Web 应用程序**并**无身份验证**。</span><span class="sxs-lookup"><span data-stu-id="4e910-122">Select **Web Application** and **No Authentication**.</span></span>

* <span data-ttu-id="4e910-123">右键单击解决方案资源管理器中的项目并选择**管理 Bower 程序包**(或者从主菜单**项目** > **管理 Bower 程序包**).</span><span class="sxs-lookup"><span data-stu-id="4e910-123">Right-click the project in Solution Explorer and select **Manage Bower Packages** (alternatively from the main menu, **Project** > **Manage Bower Packages**).</span></span>

* <span data-ttu-id="4e910-124">在中**Bower:\<项目名称\>** 窗口中，单击"浏览"选项卡，并输入筛选在包列表`font-awesome`在搜索框中：</span><span class="sxs-lookup"><span data-stu-id="4e910-124">In the **Bower: \<project name\>** window, click the "Browse" tab, and then filter the packages list by entering `font-awesome` in the search box:</span></span>

  ![管理 bower 包](bower/_static/manage-bower-packages.png)

* <span data-ttu-id="4e910-126">确认"保存更改为*bower.json*"复选框已选中。</span><span class="sxs-lookup"><span data-stu-id="4e910-126">Confirm that the "Save changes to *bower.json*" check box is checked.</span></span> <span data-ttu-id="4e910-127">从下拉列表中选择一个版本，然后单击**安装**按钮。</span><span class="sxs-lookup"><span data-stu-id="4e910-127">Select a version from the drop-down list and click the **Install** button.</span></span> <span data-ttu-id="4e910-128">**输出**窗口将显示安装详细信息。</span><span class="sxs-lookup"><span data-stu-id="4e910-128">The **Output** window shows the installation details.</span></span>

### <a name="manual-installation-in-bowerjson"></a><span data-ttu-id="4e910-129">在 bower.json 的手动安装</span><span class="sxs-lookup"><span data-stu-id="4e910-129">Manual installation in bower.json</span></span>

<span data-ttu-id="4e910-130">打开*bower.json*文件，并将"字体 awesome"添加到依赖项。</span><span class="sxs-lookup"><span data-stu-id="4e910-130">Open the *bower.json* file and add "font-awesome" to the dependencies.</span></span> <span data-ttu-id="4e910-131">IntelliSense 显示可用的包。</span><span class="sxs-lookup"><span data-stu-id="4e910-131">IntelliSense shows the available packages.</span></span> <span data-ttu-id="4e910-132">选择包后，显示可用的版本。</span><span class="sxs-lookup"><span data-stu-id="4e910-132">When a package is selected, the available versions are displayed.</span></span> <span data-ttu-id="4e910-133">下图是较旧和不匹配，请参阅。</span><span class="sxs-lookup"><span data-stu-id="4e910-133">The images below are older and won't match what you see.</span></span>

![智能感知的 bower 包资源管理器](bower/_static/add-package.png)

![bower 版本 IntelliSense](bower/_static/version-intelliSense.png)

<span data-ttu-id="4e910-136">使用 bower[语义化版本控制](https://semver.org/)来组织依赖项。</span><span class="sxs-lookup"><span data-stu-id="4e910-136">Bower uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="4e910-137">语义版本控制，也称为 SemVer，标识包具有单独的编号方案\<主要 >。\<次要 >。\<修补程序 >。</span><span class="sxs-lookup"><span data-stu-id="4e910-137">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="4e910-138">IntelliSense 显示仅几个常用的选项，从而简化了语义化版本控制。</span><span class="sxs-lookup"><span data-stu-id="4e910-138">IntelliSense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="4e910-139">智能感知列表 (在上面的示例 4.6.3) 中的顶级项被视为包的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="4e910-139">The top item in the IntelliSense list (4.6.3 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="4e910-140">插入符号 (^) 符号匹配的最新的主版本和颚化符 （~） 与最新次要版本匹配。</span><span class="sxs-lookup"><span data-stu-id="4e910-140">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span>

<span data-ttu-id="4e910-141">保存*bower.json*文件。</span><span class="sxs-lookup"><span data-stu-id="4e910-141">Save the *bower.json* file.</span></span> <span data-ttu-id="4e910-142">Visual Studio 会监视*bower.json*文件的更改。</span><span class="sxs-lookup"><span data-stu-id="4e910-142">Visual Studio watches the *bower.json* file for changes.</span></span> <span data-ttu-id="4e910-143">保存后， *bower 安装*执行命令。</span><span class="sxs-lookup"><span data-stu-id="4e910-143">Upon saving, the *bower install* command is executed.</span></span> <span data-ttu-id="4e910-144">请参见输出窗口**Bower/npm**视图执行的确切命令。</span><span class="sxs-lookup"><span data-stu-id="4e910-144">See the Output window's **Bower/npm** view for the exact command executed.</span></span>

<span data-ttu-id="4e910-145">打开 *.bowerrc*文件下*bower.json*。</span><span class="sxs-lookup"><span data-stu-id="4e910-145">Open the *.bowerrc* file under *bower.json*.</span></span> <span data-ttu-id="4e910-146">`directory`属性设置为*wwwroot/lib*指示位置 Bower 将安装的包资产。</span><span class="sxs-lookup"><span data-stu-id="4e910-146">The `directory` property is set to *wwwroot/lib* which indicates the location Bower will install the package assets.</span></span>

```json
{
 "directory": "wwwroot/lib"
}
```

<span data-ttu-id="4e910-147">可以使用解决方案资源管理器中的搜索框查找并显示字体令人惊叹的包。</span><span class="sxs-lookup"><span data-stu-id="4e910-147">You can use the search box in Solution Explorer to find and display the font-awesome package.</span></span>

<span data-ttu-id="4e910-148">打开*views/shared\_Layout.cshtml*文件，并将字体出色的 CSS 文件添加到环境[标记帮助程序](xref:mvc/views/tag-helpers/intro)为`Development`。</span><span class="sxs-lookup"><span data-stu-id="4e910-148">Open the *Views\Shared\_Layout.cshtml* file and add the font-awesome CSS file to the environment [Tag Helper](xref:mvc/views/tag-helpers/intro) for `Development`.</span></span> <span data-ttu-id="4e910-149">从解决方案资源管理器拖放*字体 awesome.css*内`<environment names="Development">`元素。</span><span class="sxs-lookup"><span data-stu-id="4e910-149">From Solution Explorer, drag and drop *font-awesome.css* inside the `<environment names="Development">` element.</span></span>

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

<span data-ttu-id="4e910-150">在生产应用中添加*字体 awesome.min.css*环境标记帮助程序对于`Staging,Production`。</span><span class="sxs-lookup"><span data-stu-id="4e910-150">In a production app you would add *font-awesome.min.css* to the environment tag helper for `Staging,Production`.</span></span>

<span data-ttu-id="4e910-151">内容替换为*Views\Home\About.cshtml* Razor 文件使用以下标记：</span><span class="sxs-lookup"><span data-stu-id="4e910-151">Replace the contents of the *Views\Home\About.cshtml* Razor file with the following markup:</span></span>

[!code-html[](bower/sample/About.cshtml)]

<span data-ttu-id="4e910-152">运行应用并导航到关于视图以确保出色字体包正常运行。</span><span class="sxs-lookup"><span data-stu-id="4e910-152">Run the app and navigate to the About view to verify the font-awesome package works.</span></span>

## <a name="exploring-the-client-side-build-process"></a><span data-ttu-id="4e910-153">浏览客户端的生成过程</span><span class="sxs-lookup"><span data-stu-id="4e910-153">Exploring the client-side build process</span></span>

<span data-ttu-id="4e910-154">大多数 ASP.NET Core 项目模板已配置为使用 Bower。</span><span class="sxs-lookup"><span data-stu-id="4e910-154">Most ASP.NET Core project templates are already configured to use Bower.</span></span> <span data-ttu-id="4e910-155">此下一个演练中创建空的 ASP.NET Core 项目启动，并手动添加每个部分，以便您可以如何在项目中使用 Bower 获取获得感觉。</span><span class="sxs-lookup"><span data-stu-id="4e910-155">This next walkthrough starts with an empty ASP.NET Core project and adds each piece manually, so you can get a feel for how Bower is used in a project.</span></span> <span data-ttu-id="4e910-156">您所见到的项目结构和运行时输出会和每个配置更改会发生什么情况。</span><span class="sxs-lookup"><span data-stu-id="4e910-156">You can see what happens to the project structure and the runtime output as each configuration change is made.</span></span>

<span data-ttu-id="4e910-157">若要与 Bower 一起使用客户端的生成过程的常规步骤如下：</span><span class="sxs-lookup"><span data-stu-id="4e910-157">The general steps to use the client-side build process with Bower are:</span></span>

* <span data-ttu-id="4e910-158">定义项目中使用的包。</span><span class="sxs-lookup"><span data-stu-id="4e910-158">Define packages used in your project.</span></span> <!-- once defined, you don't need to download them, VS does -->
* <span data-ttu-id="4e910-159">从您的 web 页面的引用包。</span><span class="sxs-lookup"><span data-stu-id="4e910-159">Reference packages from your web pages.</span></span>

### <a name="define-packages"></a><span data-ttu-id="4e910-160">定义包</span><span class="sxs-lookup"><span data-stu-id="4e910-160">Define packages</span></span>

<span data-ttu-id="4e910-161">列表中的包后*bower.json*文件，Visual Studio 将下载它们。</span><span class="sxs-lookup"><span data-stu-id="4e910-161">Once you list packages in the *bower.json* file, Visual Studio will download them.</span></span> <span data-ttu-id="4e910-162">下面的示例使用 Bower 加载 jQuery 和到 Bootstrap *wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="4e910-162">The following example uses Bower to load jQuery and Bootstrap to the *wwwroot* folder.</span></span>

* <span data-ttu-id="4e910-163">创建新的 ASP.NET Core Web 应用程序与**ASP.NET Core Web 应用程序 (.NET Core)** 模板。</span><span class="sxs-lookup"><span data-stu-id="4e910-163">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="4e910-164">选择**空**项目模板，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="4e910-164">Select the **Empty** project template and click **OK**.</span></span>

* <span data-ttu-id="4e910-165">在解决方案资源管理器中右键单击该项目 >**添加新项**，然后选择**Bower 配置文件**。</span><span class="sxs-lookup"><span data-stu-id="4e910-165">In Solution Explorer, right-click the project > **Add New Item** and select **Bower Configuration File**.</span></span> <span data-ttu-id="4e910-166">注意:一个 *.bowerrc*文件也添加。</span><span class="sxs-lookup"><span data-stu-id="4e910-166">Note: A *.bowerrc* file is also added.</span></span>

* <span data-ttu-id="4e910-167">打开*bower.json*，并添加 jquery 和启动到`dependencies`部分。</span><span class="sxs-lookup"><span data-stu-id="4e910-167">Open *bower.json*, and add jquery and bootstrap to the `dependencies` section.</span></span> <span data-ttu-id="4e910-168">得到*bower.json*文件看起来如下例所示。</span><span class="sxs-lookup"><span data-stu-id="4e910-168">The resulting *bower.json* file will look like the following example.</span></span> <span data-ttu-id="4e910-169">版本会随时间变化，可能与下面的图像不匹配。</span><span class="sxs-lookup"><span data-stu-id="4e910-169">The versions will change over time and may not match the image below.</span></span>

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* <span data-ttu-id="4e910-170">保存*bower.json*文件。</span><span class="sxs-lookup"><span data-stu-id="4e910-170">Save the *bower.json* file.</span></span>

  <span data-ttu-id="4e910-171">验证该项目包括*bootstrap*并*jQuery*中的目录*wwwroot/lib*。</span><span class="sxs-lookup"><span data-stu-id="4e910-171">Verify the project includes the *bootstrap* and *jQuery* directories in *wwwroot/lib*.</span></span> <span data-ttu-id="4e910-172">使用 bower *.bowerrc*文件以安装中的资产*wwwroot/lib*。</span><span class="sxs-lookup"><span data-stu-id="4e910-172">Bower uses the *.bowerrc* file to install the assets in *wwwroot/lib*.</span></span>

  <span data-ttu-id="4e910-173">注意:"管理 Bower 程序包"UI 提供手动文件编辑。</span><span class="sxs-lookup"><span data-stu-id="4e910-173">Note: The "Manage Bower Packages" UI provides an alternative to manual file editing.</span></span>

### <a name="enable-static-files"></a><span data-ttu-id="4e910-174">启用静态文件</span><span class="sxs-lookup"><span data-stu-id="4e910-174">Enable static files</span></span>

* <span data-ttu-id="4e910-175">添加`Microsoft.AspNetCore.StaticFiles`到项目的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4e910-175">Add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project.</span></span>
* <span data-ttu-id="4e910-176">启用静态文件提供与[静态文件中间件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)。</span><span class="sxs-lookup"><span data-stu-id="4e910-176">Enable static files to be served with the [Static file middleware](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions).</span></span> <span data-ttu-id="4e910-177">添加对的调用[UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)到`Configure`方法的`Startup`。</span><span class="sxs-lookup"><span data-stu-id="4e910-177">Add a call to [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) to the `Configure` method of `Startup`.</span></span>

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a><span data-ttu-id="4e910-178">引用包</span><span class="sxs-lookup"><span data-stu-id="4e910-178">Reference packages</span></span>

<span data-ttu-id="4e910-179">在本部分中，将创建一个 HTML 页，以验证它可以访问已部署的包。</span><span class="sxs-lookup"><span data-stu-id="4e910-179">In this section, you will create an HTML page to verify it can access the deployed packages.</span></span>

* <span data-ttu-id="4e910-180">添加一个名为的新 HTML 页*Index.html*到*wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="4e910-180">Add a new HTML page named *Index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="4e910-181">注意:必须将添加到 HTML 文件*wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="4e910-181">Note: You must add the HTML file to the *wwwroot* folder.</span></span> <span data-ttu-id="4e910-182">默认情况下，静态内容不能提供外部*wwwroot*。</span><span class="sxs-lookup"><span data-stu-id="4e910-182">By default, static content cannot be served outside *wwwroot*.</span></span> <span data-ttu-id="4e910-183">请参阅[静态文件](xref:fundamentals/static-files)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="4e910-183">See [Static files](xref:fundamentals/static-files) for more information.</span></span>

  <span data-ttu-id="4e910-184">内容替换为*Index.html*使用以下标记：</span><span class="sxs-lookup"><span data-stu-id="4e910-184">Replace the contents of *Index.html* with the following markup:</span></span>

[!code-html[](bower/sample/Index.html)]

* <span data-ttu-id="4e910-185">运行应用并导航到`http://localhost:<port>/Index.html`。</span><span class="sxs-lookup"><span data-stu-id="4e910-185">Run the app and navigate to `http://localhost:<port>/Index.html`.</span></span> <span data-ttu-id="4e910-186">或者，使用*Index.html*打开，按`Ctrl+Shift+W`。</span><span class="sxs-lookup"><span data-stu-id="4e910-186">Alternatively, with *Index.html* opened, press `Ctrl+Shift+W`.</span></span> <span data-ttu-id="4e910-187">验证应用 jumbotron 样式、 jQuery 代码进行响应时单击该按钮，并且启动按钮更改状态。</span><span class="sxs-lookup"><span data-stu-id="4e910-187">Verify that the jumbotron styling is applied, the jQuery code responds when the button is clicked, and that the Bootstrap button changes state.</span></span>

  ![应用 jumbotron 样式](bower/_static/jumbotron.png)
