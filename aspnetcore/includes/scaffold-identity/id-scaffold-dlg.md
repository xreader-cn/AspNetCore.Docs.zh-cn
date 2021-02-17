---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552337"
---
<span data-ttu-id="f4b33-101">运行 Identity scaffolder：</span><span class="sxs-lookup"><span data-stu-id="f4b33-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f4b33-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f4b33-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f4b33-103">在 **解决方案资源管理器** 中，右键单击项目 > "**添加**  >  **新的基架项**"。</span><span class="sxs-lookup"><span data-stu-id="f4b33-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f4b33-104">从 "**添加新的基架项**" 对话框的左窗格中，选择 " **Identity**  >  **添加**"。</span><span class="sxs-lookup"><span data-stu-id="f4b33-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="f4b33-105">在 "**添加 Identity** " 对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="f4b33-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="f4b33-106">选择现有的布局页，或将用错误的标记覆盖布局文件：</span><span class="sxs-lookup"><span data-stu-id="f4b33-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="f4b33-107">`~/Pages/Shared/_Layout.cshtml` 对于 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="f4b33-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="f4b33-108">`~/Views/Shared/_Layout.cshtml` 对于 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="f4b33-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="f4b33-109">Blazor ServerBlazor Server `blazorserver` 默认情况下，不会为默认情况下通过模板创建的应用 () 配置为 Razor 页面或 MVC。</span><span class="sxs-lookup"><span data-stu-id="f4b33-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="f4b33-110">将 "布局页" 项留空。</span><span class="sxs-lookup"><span data-stu-id="f4b33-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="f4b33-111">选择 **+** 按钮以创建新的 **数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="f4b33-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="f4b33-112">接受默认值或指定类 (例如 `MyApplication.Data.ApplicationDbContext`) 。</span><span class="sxs-lookup"><span data-stu-id="f4b33-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="f4b33-113">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="f4b33-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f4b33-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f4b33-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f4b33-115">如果你之前未安装 ASP.NET Core scaffolder，请立即安装：</span><span class="sxs-lookup"><span data-stu-id="f4b33-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="f4b33-116">将所需的 NuGet 包引用添加到 (*.csproj*) 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="f4b33-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="f4b33-117">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f4b33-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="f4b33-118">运行以下命令以列出 Identity scaffolder 选项：</span><span class="sxs-lookup"><span data-stu-id="f4b33-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="f4b33-119">在项目文件夹中，运行 Identity 具有所需选项的 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="f4b33-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="f4b33-120">例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f4b33-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
