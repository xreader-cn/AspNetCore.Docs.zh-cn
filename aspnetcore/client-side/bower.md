---
title: 管理 ASP.NET Core 中的 Bower 的客户端包
author: rick-anderson
description: 使用 Bower 管理客户端的包。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 08e6daa537c6c6f92a1cf80d70745e8ef606f580
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892994"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>管理 ASP.NET Core 中的 Bower 的客户端包

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Noel 大米](https://twitter.com/noelrice1)，和[Scott Addie](https://scottaddie.com)

> [!IMPORTANT]
> 而维护 Bower，其维护人员建议使用另一种解决方案。 [库管理器](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/)(简称 LibMan) 是 Visual Studio 的新客户端库获取工具 (Visual Studio 15.8 或更高版本)。 有关详细信息，请参阅 <xref:client-side/libman/index>。 Bower 在 Visual Studio 中受支持版本 15.5 通过。
>
> Yarn Webpack 使用是的其中一个受欢迎的替代方法[迁移说明](https://bower.io/blog/2017/how-to-migrate-away-from-bower/)可用。

[Bower](https://bower.io/)调用其自身"web 包管理器"。 在.NET 生态系统，它填补留下的 NuGet 的无法传送静态内容的文件。 对于 ASP.NET Core 项目，这些静态文件，则所固有的客户端库，如[jQuery](http://jquery.com/)和[Bootstrap](http://getbootstrap.com/)。 对于.NET 库，您仍使用[NuGet](https://www.nuget.org/)包管理器。

设置客户端的 ASP.NET Core 项目模板创建的新项目生成过程。 [jQuery](http://jquery.com/)并[Bootstrap](http://getbootstrap.com/)安装，并支持 Bower。

客户端的包中列出*bower.json*文件。 ASP.NET Core 项目模板配置*bower.json* jQuery、 jQuery 验证与 Bootstrap。

在本教程中，我们将添加对的支持[Font Awesome](http://fontawesome.io)。 可以使用安装 bower 程序包**管理 Bower 程序包**UI 或手动*bower.json*文件。

### <a name="installation-via-manage-bower-packages-ui"></a>通过管理 Bower 程序包 UI 安装

* 创建新的 ASP.NET Core Web 应用程序与**ASP.NET Core Web 应用程序 (.NET Core)** 模板。 选择**Web 应用程序**并**无身份验证**。

* 右键单击解决方案资源管理器中的项目并选择**管理 Bower 程序包**(或者从主菜单**项目** > **管理 Bower 程序包**).

* 在中**Bower:\<项目名称\>** 窗口中，单击"浏览"选项卡，并输入筛选在包列表`font-awesome`在搜索框中：

  ![管理 bower 包](bower/_static/manage-bower-packages.png)

* 确认"保存更改为*bower.json*"复选框已选中。 从下拉列表中选择一个版本，然后单击**安装**按钮。 **输出**窗口将显示安装详细信息。

### <a name="manual-installation-in-bowerjson"></a>在 bower.json 的手动安装

打开*bower.json*文件，并将"字体 awesome"添加到依赖项。 IntelliSense 显示可用的包。 选择包后，显示可用的版本。 下图是较旧和不匹配，请参阅。

![智能感知的 bower 包资源管理器](bower/_static/add-package.png)

![bower 版本 IntelliSense](bower/_static/version-intelliSense.png)

使用 bower[语义化版本控制](http://semver.org/)来组织依赖项。 语义版本控制，也称为 SemVer，标识包具有单独的编号方案\<主要 >。\<次要 >。\<修补程序 >。 IntelliSense 显示仅几个常用的选项，从而简化了语义化版本控制。 智能感知列表 (在上面的示例 4.6.3) 中的顶级项被视为包的最新稳定版本。 插入符号 (^) 符号匹配的最新的主版本和颚化符 （~） 与最新次要版本匹配。

保存*bower.json*文件。 Visual Studio 会监视*bower.json*文件的更改。 保存后， *bower 安装*执行命令。 请参见输出窗口**Bower/npm**视图执行的确切命令。

打开 *.bowerrc*文件下*bower.json*。 `directory`属性设置为*wwwroot/lib*指示位置 Bower 将安装的包资产。

```json
{
 "directory": "wwwroot/lib"
}
```

可以使用解决方案资源管理器中的搜索框查找并显示字体令人惊叹的包。

打开*views/shared\_Layout.cshtml*文件，并将字体出色的 CSS 文件添加到环境[标记帮助程序](xref:mvc/views/tag-helpers/intro)为`Development`。 从解决方案资源管理器拖放*字体 awesome.css*内`<environment names="Development">`元素。

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

在生产应用中添加*字体 awesome.min.css*环境标记帮助程序对于`Staging,Production`。

内容替换为*Views\Home\About.cshtml* Razor 文件使用以下标记：

[!code-html[](bower/sample/About.cshtml)]

运行应用并导航到关于视图以确保出色字体包正常运行。

## <a name="exploring-the-client-side-build-process"></a>浏览客户端的生成过程

大多数 ASP.NET Core 项目模板已配置为使用 Bower。 此下一个演练中创建空的 ASP.NET Core 项目启动，并手动添加每个部分，以便您可以如何在项目中使用 Bower 获取获得感觉。 您所见到的项目结构和运行时输出会和每个配置更改会发生什么情况。

若要与 Bower 一起使用客户端的生成过程的常规步骤如下：

* 定义项目中使用的包。 <!-- once defined, you don't need to download them, VS does -->
* 从您的 web 页面的引用包。

### <a name="define-packages"></a>定义包

列表中的包后*bower.json*文件，Visual Studio 将下载它们。 下面的示例使用 Bower 加载 jQuery 和到 Bootstrap *wwwroot*文件夹。

* 创建新的 ASP.NET Core Web 应用程序与**ASP.NET Core Web 应用程序 (.NET Core)** 模板。 选择**空**项目模板，然后单击**确定**。

* 在解决方案资源管理器中右键单击该项目 >**添加新项**，然后选择**Bower 配置文件**。 注意:一个 *.bowerrc*文件也添加。

* 打开*bower.json*，并添加 jquery 和启动到`dependencies`部分。 得到*bower.json*文件看起来如下例所示。 版本会随时间变化，可能与下面的图像不匹配。

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* 保存*bower.json*文件。

  验证该项目包括*bootstrap*并*jQuery*中的目录*wwwroot/lib*。 使用 bower *.bowerrc*文件以安装中的资产*wwwroot/lib*。

  注意:"管理 Bower 程序包"UI 提供手动文件编辑。

### <a name="enable-static-files"></a>启用静态文件

* 添加`Microsoft.AspNetCore.StaticFiles`到项目的 NuGet 包。
* 启用静态文件提供与[静态文件中间件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)。 添加对的调用[UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)到`Configure`方法的`Startup`。

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>引用包

在本部分中，将创建一个 HTML 页，以验证它可以访问已部署的包。

* 添加一个名为的新 HTML 页*Index.html*到*wwwroot*文件夹。 注意:必须将添加到 HTML 文件*wwwroot*文件夹。 默认情况下，静态内容不能提供外部*wwwroot*。 请参阅[静态文件](xref:fundamentals/static-files)有关详细信息。

  内容替换为*Index.html*使用以下标记：

[!code-html[](bower/sample/Index.html)]

* 运行应用并导航到`http://localhost:<port>/Index.html`。 或者，使用*Index.html*打开，按`Ctrl+Shift+W`。 验证应用 jumbotron 样式、 jQuery 代码进行响应时单击该按钮，并且启动按钮更改状态。

  ![应用 jumbotron 样式](bower/_static/jumbotron.png)
