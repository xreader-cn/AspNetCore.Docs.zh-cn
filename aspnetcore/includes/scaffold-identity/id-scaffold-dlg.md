<span data-ttu-id="30ea3-101">运行标识 scaffolder：</span><span class="sxs-lookup"><span data-stu-id="30ea3-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="30ea3-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30ea3-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30ea3-103">在**解决方案资源管理器**中，右键单击项目 >**添加** > 新的**基架项**。</span><span class="sxs-lookup"><span data-stu-id="30ea3-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="30ea3-104">在 "**添加基架**" 对话框的左窗格中，选择 "**标识**" > "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="30ea3-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="30ea3-105">在 "**添加标识**" 对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="30ea3-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="30ea3-106">选择现有的布局页，否则将用错误的标记覆盖你的布局文件。</span><span class="sxs-lookup"><span data-stu-id="30ea3-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="30ea3-107">例如 `~/Pages/Shared/_Layout.cshtml` Razor Pages MVC 项目的 `~/Views/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="30ea3-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="30ea3-108">选择 " **+** " 按钮以创建新的**数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="30ea3-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="30ea3-109">选择 "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="30ea3-109">Select **ADD**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="30ea3-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="30ea3-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30ea3-111">如果以前未安装 ASP.NET Core 基架，请立即进行安装：</span><span class="sxs-lookup"><span data-stu-id="30ea3-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="30ea3-112">将所需的 NuGet 包引用添加到项目（\*.csproj）文件中。</span><span class="sxs-lookup"><span data-stu-id="30ea3-112">Add required NuGet package references to the project (\*.csproj) file.</span></span> <span data-ttu-id="30ea3-113">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="30ea3-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="30ea3-114">运行以下命令以列出标识基架选项：</span><span class="sxs-lookup"><span data-stu-id="30ea3-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="30ea3-115">在项目文件夹中，运行具有所需选项的标识 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="30ea3-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="30ea3-116">例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="30ea3-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
