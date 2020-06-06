<span data-ttu-id="741fa-101">运行标识 scaffolder：</span><span class="sxs-lookup"><span data-stu-id="741fa-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fa-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fa-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="741fa-103">在**解决方案资源管理器**中，右键单击项目 > "**添加**  >  **新的基架项**"。</span><span class="sxs-lookup"><span data-stu-id="741fa-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="741fa-104">从 "**添加新的基架项**" 对话框的左窗格中，选择 "**标识**" "  >  **添加**"。</span><span class="sxs-lookup"><span data-stu-id="741fa-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="741fa-105">在 "**添加标识**" 对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="741fa-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="741fa-106">选择现有的布局页，或将用错误的标记覆盖布局文件：</span><span class="sxs-lookup"><span data-stu-id="741fa-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="741fa-107">`~/Pages/Shared/_Layout.cshtml`对于 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="741fa-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="741fa-108">`~/Views/Shared/_Layout.cshtml`对于 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="741fa-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="741fa-109">`blazorserver`默认情况下，不会为 Razor Pages 或 MVC 为 Blazor 服务器模板（）中创建的 Blazor 服务器应用配置。</span><span class="sxs-lookup"><span data-stu-id="741fa-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="741fa-110">将 "布局页" 项留空。</span><span class="sxs-lookup"><span data-stu-id="741fa-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="741fa-111">选择 **+** 按钮以创建新的**数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="741fa-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="741fa-112">接受默认值或指定类（例如 `MyApplication.Data.ApplicationDbContext` ）。</span><span class="sxs-lookup"><span data-stu-id="741fa-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="741fa-113">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="741fa-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="741fa-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="741fa-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="741fa-115">如果你之前未安装 ASP.NET Core scaffolder，请立即安装：</span><span class="sxs-lookup"><span data-stu-id="741fa-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="741fa-116">将所需的 NuGet 包引用添加到项目文件（*.csproj*）。</span><span class="sxs-lookup"><span data-stu-id="741fa-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="741fa-117">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="741fa-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="741fa-118">运行以下命令以列出 Identity scaffolder 选项：</span><span class="sxs-lookup"><span data-stu-id="741fa-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="741fa-119">在项目文件夹中，运行具有所需选项的标识 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="741fa-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="741fa-120">例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="741fa-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
