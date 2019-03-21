---
ms.openlocfilehash: 648ed8087c64eaf80fd055fa7dd08041ab9a8752
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320272"
---
<span data-ttu-id="b9ff8-101">运行标识基架：</span><span class="sxs-lookup"><span data-stu-id="b9ff8-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b9ff8-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b9ff8-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b9ff8-103">从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b9ff8-104">从左窗格**添加基架**对话框中，选择**标识** > **添加**。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="b9ff8-105">在中**ADD 标识添加**对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="b9ff8-106">选择现有的布局页上，或不正确的标记将被覆盖布局文件。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="b9ff8-107">例如`~/Pages/Shared/_Layout.cshtml`Razor 页面`~/Views/Shared/_Layout.cshtml`对于 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="b9ff8-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="b9ff8-108">选择 **+** 按钮以创建一个新**数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="b9ff8-109">选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b9ff8-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b9ff8-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b9ff8-111">如果以前未安装 ASP.NET Core 基架，请立即进行安装：</span><span class="sxs-lookup"><span data-stu-id="b9ff8-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="b9ff8-112">添加到包引用[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)到项目 (\*.csproj) 文件。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-112">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="b9ff8-113">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9ff8-113">Run the following command in the project directory:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="b9ff8-114">运行以下命令以列出标识基架选项：</span><span class="sxs-lookup"><span data-stu-id="b9ff8-114">Run the following command to list the Identity scaffolder options:</span></span>

```cli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="b9ff8-115">在项目文件夹中，运行所需的选项标识基架。</span><span class="sxs-lookup"><span data-stu-id="b9ff8-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="b9ff8-116">例如，若要设置默认值 UI 标识和最小的文件数，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b9ff8-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```cli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
