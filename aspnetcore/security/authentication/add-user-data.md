---
title: 添加、 下载和删除标识到 ASP.NET Core 项目中的用户数据
author: rick-anderson
description: 了解如何在 ASP.NET Core 项目中添加到标识的自定义用户数据。 删除每个 GDPR 的数据。
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: e08c02e2e5d4a429aae10c59e7ae3ea48c975067
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885554"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="0200e-104">添加、 下载和删除标识到 ASP.NET Core项目中的自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="0200e-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="0200e-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0200e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0200e-106">本文介绍如何：</span><span class="sxs-lookup"><span data-stu-id="0200e-106">This article shows how to:</span></span>

* <span data-ttu-id="0200e-107">将自定义用户数据添加到 ASP.NET Core web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="0200e-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="0200e-108">使用 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 特性标记自定义用户数据模型，使其自动可供下载和删除。</span><span class="sxs-lookup"><span data-stu-id="0200e-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="0200e-109">使能够下载和删除数据可帮助满足[GDPR](xref:security/gdpr)要求。</span><span class="sxs-lookup"><span data-stu-id="0200e-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="0200e-110">项目示例将创建从 Razor 页 web 应用，但了 ASP.NET Core MVC web 应用的类似的说明。</span><span class="sxs-lookup"><span data-stu-id="0200e-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="0200e-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0200e-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0200e-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="0200e-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="0200e-113">创建 Razor Web 应用</span><span class="sxs-lookup"><span data-stu-id="0200e-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0200e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0200e-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="0200e-115">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="0200e-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="0200e-116">将项目命名**WebApp1**如果你想与其匹配的命名空间[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)代码。</span><span class="sxs-lookup"><span data-stu-id="0200e-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="0200e-117">选择**ASP.NET Core Web 应用程序**>**确定**</span><span class="sxs-lookup"><span data-stu-id="0200e-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="0200e-118">在下拉列表中选择**ASP.NET Core 3.0**</span><span class="sxs-lookup"><span data-stu-id="0200e-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="0200e-119">选择**Web 应用程序**>**确定**</span><span class="sxs-lookup"><span data-stu-id="0200e-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="0200e-120">生成并运行该项目。</span><span class="sxs-lookup"><span data-stu-id="0200e-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="0200e-121">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="0200e-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="0200e-122">将项目命名**WebApp1**如果你想与其匹配的命名空间[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)代码。</span><span class="sxs-lookup"><span data-stu-id="0200e-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="0200e-123">选择**ASP.NET Core Web 应用程序**>**确定**</span><span class="sxs-lookup"><span data-stu-id="0200e-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="0200e-124">在下拉列表中选择**ASP.NET Core 2.2**</span><span class="sxs-lookup"><span data-stu-id="0200e-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="0200e-125">选择**Web 应用程序**>**确定**</span><span class="sxs-lookup"><span data-stu-id="0200e-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="0200e-126">生成并运行该项目。</span><span class="sxs-lookup"><span data-stu-id="0200e-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0200e-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0200e-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="0200e-128">运行标识基架</span><span class="sxs-lookup"><span data-stu-id="0200e-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0200e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0200e-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0200e-130">从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。</span><span class="sxs-lookup"><span data-stu-id="0200e-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0200e-131">在 "**添加基架**" 对话框的左窗格中，选择 "**标识**" > "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="0200e-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="0200e-132">在 "**添加标识**" 对话框中，选择以下选项：</span><span class="sxs-lookup"><span data-stu-id="0200e-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="0200e-133">选择现有的布局文件 *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0200e-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="0200e-134">选择要重写的以下文件：</span><span class="sxs-lookup"><span data-stu-id="0200e-134">Select the following files to override:</span></span>
    * <span data-ttu-id="0200e-135">**帐户/注册**</span><span class="sxs-lookup"><span data-stu-id="0200e-135">**Account/Register**</span></span>
    * <span data-ttu-id="0200e-136">**帐户/管理/索引**</span><span class="sxs-lookup"><span data-stu-id="0200e-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="0200e-137">选择 **+** 按钮以创建一个新**数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="0200e-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="0200e-138">接受的类型 (**WebApp1.Models.WebApp1Context**如果项目命名为**WebApp1**)。</span><span class="sxs-lookup"><span data-stu-id="0200e-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="0200e-139">选择 **+** 按钮以创建一个新**User 类**。</span><span class="sxs-lookup"><span data-stu-id="0200e-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="0200e-140">接受的类型 (**WebApp1User**如果项目命名为**WebApp1**) >**添加**。</span><span class="sxs-lookup"><span data-stu-id="0200e-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="0200e-141">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="0200e-141">Select **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0200e-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0200e-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0200e-143">如果以前未安装 ASP.NET Core 基架，请立即进行安装：</span><span class="sxs-lookup"><span data-stu-id="0200e-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="0200e-144">添加到包引用[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)项目 (.csproj) 文件。</span><span class="sxs-lookup"><span data-stu-id="0200e-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="0200e-145">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0200e-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="0200e-146">运行以下命令以列出标识基架选项：</span><span class="sxs-lookup"><span data-stu-id="0200e-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="0200e-147">在项目文件夹中，运行标识基架：</span><span class="sxs-lookup"><span data-stu-id="0200e-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="0200e-148">按照中的说明[迁移、 UseAuthentication 和布局](xref:security/authentication/scaffold-identity#efm)来执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="0200e-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="0200e-149">创建迁移并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="0200e-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="0200e-150">将 `UseAuthentication` 添加到 `Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="0200e-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="0200e-151">添加`<partial name="_LoginPartial" />`布局文件。</span><span class="sxs-lookup"><span data-stu-id="0200e-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="0200e-152">测试应用：</span><span class="sxs-lookup"><span data-stu-id="0200e-152">Test the app:</span></span>
  * <span data-ttu-id="0200e-153">注册用户</span><span class="sxs-lookup"><span data-stu-id="0200e-153">Register a user</span></span>
  * <span data-ttu-id="0200e-154">选择新的用户名称 (旁边**注销**链接)。</span><span class="sxs-lookup"><span data-stu-id="0200e-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="0200e-155">您可能需要展开窗口或选择要显示的用户名称和其他链接的导航栏图标。</span><span class="sxs-lookup"><span data-stu-id="0200e-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="0200e-156">选择**个人数据**选项卡。</span><span class="sxs-lookup"><span data-stu-id="0200e-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="0200e-157">选择**下载**按钮，然后检查*PersonalData.json*文件。</span><span class="sxs-lookup"><span data-stu-id="0200e-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="0200e-158">测试**删除**按钮，删除已登录用户。</span><span class="sxs-lookup"><span data-stu-id="0200e-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="0200e-159">向标识数据库中添加自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="0200e-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="0200e-160">更新`IdentityUser`派生类使用自定义属性。</span><span class="sxs-lookup"><span data-stu-id="0200e-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="0200e-161">如果项目 WebApp1 命名为，将该文件命名*Areas/Identity/Data/WebApp1User.cs*。</span><span class="sxs-lookup"><span data-stu-id="0200e-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="0200e-162">使用以下代码更新文件：</span><span class="sxs-lookup"><span data-stu-id="0200e-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="0200e-163">具有[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)属性的属性为：</span><span class="sxs-lookup"><span data-stu-id="0200e-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="0200e-164">时删除*Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor 页面调用`UserManager.Delete`。</span><span class="sxs-lookup"><span data-stu-id="0200e-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="0200e-165">通过下载的数据中包含*Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="0200e-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="0200e-166">更新 Account/Manage/Index.cshtml 页</span><span class="sxs-lookup"><span data-stu-id="0200e-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="0200e-167">更新`InputModel`中*Areas/Identity/Pages/Account/Manage/Index.cshtml.cs*用以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="0200e-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="0200e-168">更新*Areas/Identity/Pages/Account/Manage/Index.cshtml*与以下突出显示的标记：</span><span class="sxs-lookup"><span data-stu-id="0200e-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="0200e-169">更新*Areas/Identity/Pages/Account/Manage/Index.cshtml*与以下突出显示的标记：</span><span class="sxs-lookup"><span data-stu-id="0200e-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="0200e-170">更新 account/Register.cshtml 页面</span><span class="sxs-lookup"><span data-stu-id="0200e-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="0200e-171">更新`InputModel`中*Areas/Identity/Pages/Account/Register.cshtml.cs*用以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="0200e-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="0200e-172">更新*Areas/Identity/Pages/Account/Register.cshtml*与以下突出显示的标记：</span><span class="sxs-lookup"><span data-stu-id="0200e-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="0200e-173">更新*Areas/Identity/Pages/Account/Register.cshtml*与以下突出显示的标记：</span><span class="sxs-lookup"><span data-stu-id="0200e-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="0200e-174">生成此项目。</span><span class="sxs-lookup"><span data-stu-id="0200e-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="0200e-175">添加自定义用户数据的迁移</span><span class="sxs-lookup"><span data-stu-id="0200e-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0200e-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0200e-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0200e-177">在 Visual Studio**程序包管理器控制台**:</span><span class="sxs-lookup"><span data-stu-id="0200e-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0200e-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0200e-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="0200e-179">测试创建、 查看、 下载和删除自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="0200e-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="0200e-180">测试应用：</span><span class="sxs-lookup"><span data-stu-id="0200e-180">Test the app:</span></span>

* <span data-ttu-id="0200e-181">注册一个新用户。</span><span class="sxs-lookup"><span data-stu-id="0200e-181">Register a new user.</span></span>
* <span data-ttu-id="0200e-182">查看自定义用户数据`/Identity/Account/Manage`页。</span><span class="sxs-lookup"><span data-stu-id="0200e-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="0200e-183">下载并查看用户个人数据从`/Identity/Account/Manage/PersonalData`页。</span><span class="sxs-lookup"><span data-stu-id="0200e-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
