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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551857"
---
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43dfe-101">运行 Identity scaffolder：</span><span class="sxs-lookup"><span data-stu-id="43dfe-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="43dfe-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43dfe-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43dfe-103">在 **解决方案资源管理器** 中，右键单击项目 > " **添加** > **新的基架项**"。</span><span class="sxs-lookup"><span data-stu-id="43dfe-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="43dfe-104">在 " **添加基架** " 对话框的左窗格中，选择 " **Identity** > **添加**"。</span><span class="sxs-lookup"><span data-stu-id="43dfe-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="43dfe-105">在 "**添加 Identity** " 对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="43dfe-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="43dfe-106">选择现有的布局页，以便不会用错误的标记覆盖布局文件。</span><span class="sxs-lookup"><span data-stu-id="43dfe-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="43dfe-107">如果选择了现有的 *\_ 布局 cshtml* 文件，则 **不** 会覆盖它。</span><span class="sxs-lookup"><span data-stu-id="43dfe-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="43dfe-108">例如：</span><span class="sxs-lookup"><span data-stu-id="43dfe-108">For example:</span></span>
    * <span data-ttu-id="43dfe-109">`~/Pages/Shared/_Layout.cshtml` 对于 Razor Blazor Server 包含现有 Razor 页面的基础结构的页面或项目</span><span class="sxs-lookup"><span data-stu-id="43dfe-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="43dfe-110">`~/Views/Shared/_Layout.cshtml` 对于 Blazor Server 包含现有 mvc 基础结构的 mvc 项目或项目</span><span class="sxs-lookup"><span data-stu-id="43dfe-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="43dfe-111">若要使用现有的数据上下文，请至少选择一个要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="43dfe-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="43dfe-112">必须至少选择一个文件以添加数据上下文。</span><span class="sxs-lookup"><span data-stu-id="43dfe-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="43dfe-113">选择数据上下文类。</span><span class="sxs-lookup"><span data-stu-id="43dfe-113">Select your data context class.</span></span>
  * <span data-ttu-id="43dfe-114">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="43dfe-114">Select **Add**.</span></span>
* <span data-ttu-id="43dfe-115">若要创建新的用户上下文，并可能创建的自定义用户类 Identity ：</span><span class="sxs-lookup"><span data-stu-id="43dfe-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="43dfe-116">选择 **+** 按钮以创建新的 **数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="43dfe-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="43dfe-117">接受默认值或指定类 (例如 `MyApplication.Data.ApplicationDbContext`) 。</span><span class="sxs-lookup"><span data-stu-id="43dfe-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="43dfe-118">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="43dfe-118">Select **Add**.</span></span>

<span data-ttu-id="43dfe-119">注意：如果要创建新的用户上下文，则无需选择要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="43dfe-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="43dfe-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="43dfe-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43dfe-121">如果你之前未安装 ASP.NET Core scaffolder，请立即安装：</span><span class="sxs-lookup"><span data-stu-id="43dfe-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="43dfe-122">将所需的 NuGet 包引用添加到 (*.csproj*) 的项目文件。</span><span class="sxs-lookup"><span data-stu-id="43dfe-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="43dfe-123">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="43dfe-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="43dfe-124">运行以下命令以列出 Identity scaffolder 选项：</span><span class="sxs-lookup"><span data-stu-id="43dfe-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="43dfe-125">在项目文件夹中，运行 Identity 具有所需选项的 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="43dfe-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="43dfe-126">例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="43dfe-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="43dfe-127">为数据库上下文使用正确的完全限定名：</span><span class="sxs-lookup"><span data-stu-id="43dfe-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="43dfe-128">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="43dfe-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="43dfe-129">使用 PowerShell 时，请在文件列表中转义分号，或将文件列表置于双引号中。</span><span class="sxs-lookup"><span data-stu-id="43dfe-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="43dfe-130">例如：</span><span class="sxs-lookup"><span data-stu-id="43dfe-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="43dfe-131">如果在 Identity 未指定标志或标志的情况下运行 scaffolder `--files` `--useDefaultUI` ，则 Identity 会在项目中创建所有可用的 UI 页。</span><span class="sxs-lookup"><span data-stu-id="43dfe-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="43dfe-132">运行 Identity scaffolder：</span><span class="sxs-lookup"><span data-stu-id="43dfe-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="43dfe-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43dfe-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43dfe-134">在 **解决方案资源管理器** 中，右键单击项目 > " **添加** > **新的基架项**"。</span><span class="sxs-lookup"><span data-stu-id="43dfe-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="43dfe-135">在 " **添加基架** " 对话框的左窗格中，选择 " **Identity** > **添加**"。</span><span class="sxs-lookup"><span data-stu-id="43dfe-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="43dfe-136">在 "**添加 Identity** " 对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="43dfe-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="43dfe-137">选择现有的布局页，否则将用错误的标记覆盖你的布局文件。</span><span class="sxs-lookup"><span data-stu-id="43dfe-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="43dfe-138">如果选择了现有的 *\_ 布局 cshtml* 文件，则 **不** 会覆盖它。</span><span class="sxs-lookup"><span data-stu-id="43dfe-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="43dfe-139">例如：</span><span class="sxs-lookup"><span data-stu-id="43dfe-139">For example:</span></span>
    * <span data-ttu-id="43dfe-140">`~/Pages/Shared/_Layout.cshtml` 对于 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="43dfe-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="43dfe-141">`~/Views/Shared/_Layout.cshtml` 对于 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="43dfe-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="43dfe-142">若要使用现有的数据上下文，请至少选择一个要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="43dfe-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="43dfe-143">必须至少选择一个文件以添加数据上下文。</span><span class="sxs-lookup"><span data-stu-id="43dfe-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="43dfe-144">选择数据上下文类。</span><span class="sxs-lookup"><span data-stu-id="43dfe-144">Select your data context class.</span></span>
  * <span data-ttu-id="43dfe-145">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="43dfe-145">Select **Add**.</span></span>
* <span data-ttu-id="43dfe-146">若要创建新的用户上下文，并可能创建的自定义用户类 Identity ：</span><span class="sxs-lookup"><span data-stu-id="43dfe-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="43dfe-147">选择 **+** 按钮以创建新的 **数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="43dfe-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="43dfe-148">接受默认值或指定类 (例如 `MyApplication.Data.ApplicationDbContext`) 。</span><span class="sxs-lookup"><span data-stu-id="43dfe-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="43dfe-149">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="43dfe-149">Select **Add**.</span></span>

<span data-ttu-id="43dfe-150">注意：如果要创建新的用户上下文，则无需选择要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="43dfe-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="43dfe-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="43dfe-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43dfe-152">如果你之前未安装 ASP.NET Core scaffolder，请立即安装：</span><span class="sxs-lookup"><span data-stu-id="43dfe-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="43dfe-153">将对 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 的包引用添加到项目文件 (*.csproj*) 。</span><span class="sxs-lookup"><span data-stu-id="43dfe-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="43dfe-154">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="43dfe-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="43dfe-155">运行以下命令以列出 Identity scaffolder 选项：</span><span class="sxs-lookup"><span data-stu-id="43dfe-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="43dfe-156">在项目文件夹中，运行 Identity 具有所需选项的 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="43dfe-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="43dfe-157">例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="43dfe-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="43dfe-158">为数据库上下文使用正确的完全限定名：</span><span class="sxs-lookup"><span data-stu-id="43dfe-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="43dfe-159">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="43dfe-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="43dfe-160">使用 PowerShell 时，请在文件列表中转义分号，或将文件列表置于双引号中。</span><span class="sxs-lookup"><span data-stu-id="43dfe-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="43dfe-161">例如：</span><span class="sxs-lookup"><span data-stu-id="43dfe-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="43dfe-162">如果在 Identity 未指定标志或标志的情况下运行 scaffolder `--files` `--useDefaultUI` ，则 Identity 会在项目中创建所有可用的 UI 页。</span><span class="sxs-lookup"><span data-stu-id="43dfe-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
