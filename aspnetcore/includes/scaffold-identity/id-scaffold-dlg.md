<span data-ttu-id="69cb0-101">运行标识 scaffolder:</span><span class="sxs-lookup"><span data-stu-id="69cb0-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="69cb0-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69cb0-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="69cb0-103">从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。</span><span class="sxs-lookup"><span data-stu-id="69cb0-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="69cb0-104">从左窗格**添加基架**对话框中，选择**标识** > **添加**。</span><span class="sxs-lookup"><span data-stu-id="69cb0-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="69cb0-105">在 "**添加标识**" 对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="69cb0-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="69cb0-106">选择现有的布局页, 否则将用错误的标记覆盖你的布局文件。</span><span class="sxs-lookup"><span data-stu-id="69cb0-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="69cb0-107">例如，对于 MVC 项目 Razor Pages `~/Views/Shared/_Layout.cshtml` `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="69cb0-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="69cb0-108">选择 **+** 按钮以创建一个新**数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="69cb0-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="69cb0-109">选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="69cb0-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="69cb0-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="69cb0-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="69cb0-111">如果以前未安装 ASP.NET Core 基架，请立即进行安装：</span><span class="sxs-lookup"><span data-stu-id="69cb0-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="69cb0-112">将对[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)的包引用添加到项目 (\*.csproj) 文件中。</span><span class="sxs-lookup"><span data-stu-id="69cb0-112">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="69cb0-113">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="69cb0-113">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="69cb0-114">运行以下命令以列出标识基架选项：</span><span class="sxs-lookup"><span data-stu-id="69cb0-114">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="69cb0-115">在项目文件夹中, 运行具有所需选项的标识 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="69cb0-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="69cb0-116">例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="69cb0-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
