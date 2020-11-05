---
title: 使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure
author: rick-anderson
description: 了解如何使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure 应用服务。
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060217"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="20f12-103">使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="20f12-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="20f12-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="20f12-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="20f12-105">若使用的是 macOS，请参阅[使用 Visual Studio for Mac 将 Web 应用发布到 Azure 应用服务](/visualstudio/mac/publish-app-svc?view=vsmac-2019)。</span><span class="sxs-lookup"><span data-stu-id="20f12-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="20f12-106">若要对应用服务部署问题进行故障排除，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="20f12-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="20f12-107">设置</span><span class="sxs-lookup"><span data-stu-id="20f12-107">Set up</span></span>

* <span data-ttu-id="20f12-108">如果没有[免费 Azure 帐户](https://azure.microsoft.com/free/dotnet/)，请开设一个。</span><span class="sxs-lookup"><span data-stu-id="20f12-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="20f12-109">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="20f12-109">Create a web app</span></span>

<span data-ttu-id="20f12-110">在 Visual Studio 起始页中，选择“文件”>“新建”>“项目...”</span><span class="sxs-lookup"><span data-stu-id="20f12-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![“文件”菜单](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="20f12-112">填写“新建项目”对话框：</span><span class="sxs-lookup"><span data-stu-id="20f12-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="20f12-113">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="20f12-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="20f12-114">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="20f12-114">Select **Next**.</span></span>

![“新建项目”对话框](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="20f12-116">在“新建 ASP.NET Core Web 应用程序”对话框中：</span><span class="sxs-lookup"><span data-stu-id="20f12-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="20f12-117">选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="20f12-117">Select **Web Application**.</span></span>
* <span data-ttu-id="20f12-118">选择“身份验证”下的“更改”。</span><span class="sxs-lookup"><span data-stu-id="20f12-118">Select **Change** under Authentication.</span></span>

![“新建 ASP.NET Core Web”对话框](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="20f12-120">“更改身份验证”对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="20f12-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="20f12-121">选择“个人用户帐户”。</span><span class="sxs-lookup"><span data-stu-id="20f12-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="20f12-122">选择“确定”返回到“新建 ASP.NET Core Web 应用程序”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="20f12-122">Select **OK** to return to the **New ASP.NET Core Web Application** , then select **Create**.</span></span>

![“新建 ASP.NET Core Web 身份验证”对话框](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="20f12-124">Visual Studio 随即创建解决方案。</span><span class="sxs-lookup"><span data-stu-id="20f12-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="20f12-125">运行应用</span><span class="sxs-lookup"><span data-stu-id="20f12-125">Run the app</span></span>

* <span data-ttu-id="20f12-126">按 Ctrl+F5 运行项目。</span><span class="sxs-lookup"><span data-stu-id="20f12-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="20f12-127">测试“隐私”链接。</span><span class="sxs-lookup"><span data-stu-id="20f12-127">Test the **Privacy** link.</span></span>

![localhost 上的 Microsoft Edge 中打开的 Web 应用程序](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="20f12-129">注册用户</span><span class="sxs-lookup"><span data-stu-id="20f12-129">Register a user</span></span>

* <span data-ttu-id="20f12-130">选择“注册”并注册新用户。</span><span class="sxs-lookup"><span data-stu-id="20f12-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="20f12-131">可使用虚构电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="20f12-131">You can use a fictitious email address.</span></span> <span data-ttu-id="20f12-132">提交时，页面上会显示以下错误：</span><span class="sxs-lookup"><span data-stu-id="20f12-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="20f12-133">“处理请求时，数据库操作失败。 *可通过向应用程序数据库上下文应用现有迁移解决此问题。”*</span><span class="sxs-lookup"><span data-stu-id="20f12-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="20f12-134">选择“应用迁移”，并在页面更新后刷新页面。</span><span class="sxs-lookup"><span data-stu-id="20f12-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![处理请求时，数据库操作失败。](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="20f12-137">应用将显示用于注册新用户的电子邮件和一个“注销”链接。</span><span class="sxs-lookup"><span data-stu-id="20f12-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Microsoft Edge 中打开的 Web 应用程序。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="20f12-140">将应用部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="20f12-140">Deploy the app to Azure</span></span>

<span data-ttu-id="20f12-141">在解决方案资源管理器中右键单击该项目，然后选择“发布...”。</span><span class="sxs-lookup"><span data-stu-id="20f12-141">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![打开且突出显示“发布”链接的上下文菜单](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="20f12-143">在“发布”对话框中：</span><span class="sxs-lookup"><span data-stu-id="20f12-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="20f12-144">选择“Azure”。</span><span class="sxs-lookup"><span data-stu-id="20f12-144">Select **Azure**.</span></span>
* <span data-ttu-id="20f12-145">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="20f12-145">Select **Next**.</span></span>

![“发布”对话框](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="20f12-147">在“发布”对话框中：</span><span class="sxs-lookup"><span data-stu-id="20f12-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="20f12-148">选择“Azure 应用服务 (Linux)”。</span><span class="sxs-lookup"><span data-stu-id="20f12-148">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="20f12-149">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="20f12-149">Select **Next**.</span></span>

![“发布”对话框：选择 Azure 服务](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="20f12-151">在“发布”对话框中，选择“创建新的 Azure 应用服务...”</span><span class="sxs-lookup"><span data-stu-id="20f12-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![“发布”对话框：选择 Azure 服务实例](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="20f12-153">“创建应用服务”对话框随即显示：</span><span class="sxs-lookup"><span data-stu-id="20f12-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="20f12-154">“应用名称”、“资源组”和“应用服务计划”输入字段已填充  。</span><span class="sxs-lookup"><span data-stu-id="20f12-154">The **App Name** , **Resource Group** , and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="20f12-155">可以保留这些名称，也可以进行更改。</span><span class="sxs-lookup"><span data-stu-id="20f12-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="20f12-156">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="20f12-156">Select **Create**.</span></span>

![“创建应用服务”对话框](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="20f12-158">创建完成后，对话框将自动关闭，“发布”对话框将再次成为焦点：</span><span class="sxs-lookup"><span data-stu-id="20f12-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="20f12-159">将自动选择刚创建的新实例。</span><span class="sxs-lookup"><span data-stu-id="20f12-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="20f12-160">选择“完成”。</span><span class="sxs-lookup"><span data-stu-id="20f12-160">Select **Finish**.</span></span>

![“发布”对话框：选择应用服务实例](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="20f12-162">接下来，你将看到“发布配置文件摘要”页。</span><span class="sxs-lookup"><span data-stu-id="20f12-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="20f12-163">Visual Studio 检测到此应用程序需要 SQL Server 数据库，并要求你进行配置。</span><span class="sxs-lookup"><span data-stu-id="20f12-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="20f12-164">选择“配置”。</span><span class="sxs-lookup"><span data-stu-id="20f12-164">Select **Configure**.</span></span>

![“发布配置文件摘要”页：配置 SQL Server 依赖项](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="20f12-166">“配置依赖项”对话框随即出现：</span><span class="sxs-lookup"><span data-stu-id="20f12-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="20f12-167">选择“Azure SQL 数据库”。</span><span class="sxs-lookup"><span data-stu-id="20f12-167">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="20f12-168">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="20f12-168">Select **Next**.</span></span>

![“配置 SQL Server 依赖项”对话框](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="20f12-170">在“配置 Azure SQL 数据库”对话框中选择“创建 SQL 数据库”</span><span class="sxs-lookup"><span data-stu-id="20f12-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![“配置 Azure SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="20f12-172">随即出现“创建 Azure SQL 数据库”：</span><span class="sxs-lookup"><span data-stu-id="20f12-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="20f12-173">“数据库名称”、“资源组”、“数据库服务器”、“应用服务计划”输入字段已填充。</span><span class="sxs-lookup"><span data-stu-id="20f12-173">The **Database name** , **Resource Group** , **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="20f12-174">可以保留这些值，也可以进行更改。</span><span class="sxs-lookup"><span data-stu-id="20f12-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="20f12-175">为所选“数据库服务器”输入“数据库管理员用户名”和“数据库管理员密码”（请注意，你使用的帐户必须具有创建新 Azure SQL 数据库所需的权限）</span><span class="sxs-lookup"><span data-stu-id="20f12-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="20f12-176">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="20f12-176">Select **Create**.</span></span>

![“新建 Azure SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="20f12-178">创建完成后，对话框将自动关闭，“配置 Azure SQL 数据库”对话框将再次成为焦点：</span><span class="sxs-lookup"><span data-stu-id="20f12-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="20f12-179">将自动选择刚创建的新实例。</span><span class="sxs-lookup"><span data-stu-id="20f12-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="20f12-180">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="20f12-180">Select **Next**.</span></span>

![“配置 Azure SQL 数据库”对话框](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="20f12-182">在“配置 Azure SQL 数据库”对话框的下一步中：</span><span class="sxs-lookup"><span data-stu-id="20f12-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="20f12-183">输入“数据库连接用户名”和“数据库连接密码”字段。</span><span class="sxs-lookup"><span data-stu-id="20f12-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="20f12-184">应用程序将使用这些详细信息在运行时连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="20f12-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="20f12-185">最佳做法是，避免使用与上一步中的管理员用户名和密码相同的详细信息。</span><span class="sxs-lookup"><span data-stu-id="20f12-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="20f12-186">选择“完成”。</span><span class="sxs-lookup"><span data-stu-id="20f12-186">Select **Finish**.</span></span>

![“配置 Azure SQL 数据库”对话框，连接字符串详细信息](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="20f12-188">在“发布配置文件摘要”页中选择“设置”：</span><span class="sxs-lookup"><span data-stu-id="20f12-188">In the **Publish Profile summary** page select **Settings** :</span></span>

![“发布配置文件摘要”页：编辑设置](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="20f12-190">在“发布”对话框的“设置”页面上 ：</span><span class="sxs-lookup"><span data-stu-id="20f12-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="20f12-191">展开“数据库”并选中“在运行时使用此连接字符串” 。</span><span class="sxs-lookup"><span data-stu-id="20f12-191">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="20f12-192">展开“Entity Framework 迁移”并选中“在发布时应用此迁移” 。</span><span class="sxs-lookup"><span data-stu-id="20f12-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="20f12-193">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="20f12-193">Select **Save**.</span></span> <span data-ttu-id="20f12-194">Visual Studio 将返回到“发布”对话框。</span><span class="sxs-lookup"><span data-stu-id="20f12-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![“发布”对话框：设置面板](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="20f12-196">单击“发布” 。</span><span class="sxs-lookup"><span data-stu-id="20f12-196">Click **Publish**.</span></span> <span data-ttu-id="20f12-197">Visual Studio 将应用发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="20f12-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="20f12-198">部署完成时，应用在浏览器中打开。</span><span class="sxs-lookup"><span data-stu-id="20f12-198">When the deployment completes, the app is opened in a browser.</span></span>

![“发布”对话框：设置面板](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="20f12-200">更新应用</span><span class="sxs-lookup"><span data-stu-id="20f12-200">Update the app</span></span>

* <span data-ttu-id="20f12-201">编辑 Pages/Index.cshtml :::no-loc(Razor)::: 页并更改其内容。</span><span class="sxs-lookup"><span data-stu-id="20f12-201">Edit the *Pages/Index.cshtml* :::no-loc(Razor)::: page and change its contents.</span></span> <span data-ttu-id="20f12-202">例如，可以将段落修改为显示“Hello ASP.NET Core!”：</span><span class="sxs-lookup"><span data-stu-id="20f12-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="20f12-203">再次选择“发布配置文件摘要”页中的“发布”。</span><span class="sxs-lookup"><span data-stu-id="20f12-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![“发布配置文件摘要”页](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="20f12-205">应用发布后，验证所做的更改在 Azure 上是否可用。</span><span class="sxs-lookup"><span data-stu-id="20f12-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![验证任务已完成](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="20f12-207">清理</span><span class="sxs-lookup"><span data-stu-id="20f12-207">Clean up</span></span>

<span data-ttu-id="20f12-208">完成应用测试后，转到 [Azure 门户](https://portal.azure.com/)并删除该应用。</span><span class="sxs-lookup"><span data-stu-id="20f12-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="20f12-209">选择“资源组”，然后选择所创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="20f12-209">Select **Resource groups** , then select the resource group you created.</span></span>

![Azure 门户：侧边栏菜单中的资源组](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="20f12-211">在“资源组”页面中，选择“删除” 。</span><span class="sxs-lookup"><span data-stu-id="20f12-211">In the **Resource groups** page, select **Delete**.</span></span>

![Azure 门户：“资源组”页](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="20f12-213">输入资源组的名称并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="20f12-213">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="20f12-214">现已从 Azure 中删除了本教程中创建的应用和其他所有资源。</span><span class="sxs-lookup"><span data-stu-id="20f12-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="20f12-215">后续步骤</span><span class="sxs-lookup"><span data-stu-id="20f12-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="20f12-216">其他资源</span><span class="sxs-lookup"><span data-stu-id="20f12-216">Additional resources</span></span>

* <span data-ttu-id="20f12-217">对于 Visual Studio Code，请参阅[发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="20f12-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="20f12-218">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="20f12-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="20f12-219">Azure 资源组</span><span class="sxs-lookup"><span data-stu-id="20f12-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="20f12-220">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="20f12-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>