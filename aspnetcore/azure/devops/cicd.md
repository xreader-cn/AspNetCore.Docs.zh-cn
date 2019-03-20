---
title: 持续集成和部署-使用 ASP.NET Core 和 Azure 进行开发运营
author: CamSoper
description: 持续集成和 DevOps 中使用 ASP.NET Core 和 Azure 的部署
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 676620b5dd151c9cd009d7cb278ed2c2b122c83f
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264887"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="419e5-103">持续集成和部署</span><span class="sxs-lookup"><span data-stu-id="419e5-103">Continuous integration and deployment</span></span>

<span data-ttu-id="419e5-104">在上一章，您创建简单的源读取器应用的本地 Git 存储库。</span><span class="sxs-lookup"><span data-stu-id="419e5-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="419e5-105">在本章中，会将该代码发布到 GitHub 存储库并构建使用 Azure 管道的 Azure DevOps 服务管道。</span><span class="sxs-lookup"><span data-stu-id="419e5-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="419e5-106">管道可使持续生成和部署的应用程序。</span><span class="sxs-lookup"><span data-stu-id="419e5-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="419e5-107">GitHub 存储库的任何提交触发生成和部署到 Azure Web 应用的过渡槽。</span><span class="sxs-lookup"><span data-stu-id="419e5-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="419e5-108">在本部分中，将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="419e5-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="419e5-109">将应用程序的代码发布到 GitHub</span><span class="sxs-lookup"><span data-stu-id="419e5-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="419e5-110">断开连接本地 Git 部署</span><span class="sxs-lookup"><span data-stu-id="419e5-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="419e5-111">创建 Azure DevOps 组织</span><span class="sxs-lookup"><span data-stu-id="419e5-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="419e5-112">在 Azure DevOps 服务中创建团队项目</span><span class="sxs-lookup"><span data-stu-id="419e5-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="419e5-113">创建生成定义</span><span class="sxs-lookup"><span data-stu-id="419e5-113">Create a build definition</span></span>
* <span data-ttu-id="419e5-114">创建发布管道</span><span class="sxs-lookup"><span data-stu-id="419e5-114">Create a release pipeline</span></span>
* <span data-ttu-id="419e5-115">将更改提交到 GitHub 并自动部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="419e5-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="419e5-116">检查 Azure 管道管道</span><span class="sxs-lookup"><span data-stu-id="419e5-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="419e5-117">将应用程序的代码发布到 GitHub</span><span class="sxs-lookup"><span data-stu-id="419e5-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="419e5-118">打开浏览器窗口，并导航到`https://github.com`。</span><span class="sxs-lookup"><span data-stu-id="419e5-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="419e5-119">单击 **+** 标头中的下拉列表中，选择**新的存储库**:</span><span class="sxs-lookup"><span data-stu-id="419e5-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![新的 GitHub 存储库选项](media/cicd/github-new-repo.png)

1. <span data-ttu-id="419e5-121">选择你的帐户**所有者**下拉列表中，并输入*简单源阅读器*中**存储库名称**文本框中。</span><span class="sxs-lookup"><span data-stu-id="419e5-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="419e5-122">单击**创建存储库**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="419e5-123">打开本地计算机的命令行界面。</span><span class="sxs-lookup"><span data-stu-id="419e5-123">Open your local machine's command shell.</span></span> <span data-ttu-id="419e5-124">导航到在其中的目录*简单源阅读器*存储 Git 存储库。</span><span class="sxs-lookup"><span data-stu-id="419e5-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="419e5-125">重命名现有*原点*到远程*上游*。</span><span class="sxs-lookup"><span data-stu-id="419e5-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="419e5-126">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="419e5-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="419e5-127">添加一个新*原点*远程指向您的 GitHub 上的存储库副本。</span><span class="sxs-lookup"><span data-stu-id="419e5-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="419e5-128">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="419e5-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="419e5-129">将本地 Git 存储库发布到新创建的 GitHub 存储库。</span><span class="sxs-lookup"><span data-stu-id="419e5-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="419e5-130">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="419e5-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="419e5-131">打开浏览器窗口，并导航到`https://github.com/<GitHub_username>/simple-feed-reader/`。</span><span class="sxs-lookup"><span data-stu-id="419e5-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="419e5-132">验证你的代码会在 GitHub 存储库中。</span><span class="sxs-lookup"><span data-stu-id="419e5-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="419e5-133">断开连接本地 Git 部署</span><span class="sxs-lookup"><span data-stu-id="419e5-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="419e5-134">删除本地 Git 部署通过执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="419e5-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="419e5-135">Azure 管道 （Azure DevOps 服务） 都将替换和增强该功能。</span><span class="sxs-lookup"><span data-stu-id="419e5-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="419e5-136">打开[Azure 门户](https://portal.azure.com/)，并导航到*暂存 (mywebapp\<unique_number\>/暂存)* Web 应用。</span><span class="sxs-lookup"><span data-stu-id="419e5-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="419e5-137">可以通过输入快速位于 Web 应用*暂存*门户的搜索框中：</span><span class="sxs-lookup"><span data-stu-id="419e5-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![过渡 Web 应用搜索词](media/cicd/portal-search-box.png)

1. <span data-ttu-id="419e5-139">单击**部署中心**。</span><span class="sxs-lookup"><span data-stu-id="419e5-139">Click **Deployment Center**.</span></span> <span data-ttu-id="419e5-140">将显示新面板。</span><span class="sxs-lookup"><span data-stu-id="419e5-140">A new panel appears.</span></span> <span data-ttu-id="419e5-141">单击**断开连接**来删除已添加在上一章中的本地 Git 源控件配置。</span><span class="sxs-lookup"><span data-stu-id="419e5-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="419e5-142">通过单击确认删除操作**是**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="419e5-143">导航到*mywebapp < unique_number >* 应用服务。</span><span class="sxs-lookup"><span data-stu-id="419e5-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="419e5-144">请注意，可以使用门户的搜索框中快速找到应用服务。</span><span class="sxs-lookup"><span data-stu-id="419e5-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="419e5-145">单击**部署中心**。</span><span class="sxs-lookup"><span data-stu-id="419e5-145">Click **Deployment Center**.</span></span> <span data-ttu-id="419e5-146">将显示新面板。</span><span class="sxs-lookup"><span data-stu-id="419e5-146">A new panel appears.</span></span> <span data-ttu-id="419e5-147">单击**断开连接**来删除已添加在上一章中的本地 Git 源控件配置。</span><span class="sxs-lookup"><span data-stu-id="419e5-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="419e5-148">通过单击确认删除操作**是**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="419e5-149">创建 Azure DevOps 组织</span><span class="sxs-lookup"><span data-stu-id="419e5-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="419e5-150">打开浏览器并导航到[Azure DevOps 的组织创建页](https://go.microsoft.com/fwlink/?LinkId=307137)。</span><span class="sxs-lookup"><span data-stu-id="419e5-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="419e5-151">键入唯一名称**选取一个易记名称**文本框中以形成用于访问你的 Azure DevOps 组织的 URL。</span><span class="sxs-lookup"><span data-stu-id="419e5-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="419e5-152">选择**Git**单选按钮，因为代码托管在 GitHub 存储库。</span><span class="sxs-lookup"><span data-stu-id="419e5-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="419e5-153">单击“继续”按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-153">Click the **Continue** button.</span></span> <span data-ttu-id="419e5-154">稍等片刻、 帐户和团队项目之后, 名为*MyFirstProject*，创建。</span><span class="sxs-lookup"><span data-stu-id="419e5-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Azure DevOps 的组织创建页](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="419e5-156">打开指示 Azure DevOps 的组织和项目可供使用的确认电子邮件。</span><span class="sxs-lookup"><span data-stu-id="419e5-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="419e5-157">单击**开始你的项目**按钮：</span><span class="sxs-lookup"><span data-stu-id="419e5-157">Click the **Start your project** button:</span></span>

    ![启动项目按钮](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="419e5-159">浏览器将打开 *\<account_name\>。 visualstudio.com*。</span><span class="sxs-lookup"><span data-stu-id="419e5-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="419e5-160">单击*MyFirstProject*链接以开始配置项目的 DevOps 管道。</span><span class="sxs-lookup"><span data-stu-id="419e5-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="419e5-161">配置 Azure 管道管道</span><span class="sxs-lookup"><span data-stu-id="419e5-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="419e5-162">有三个不同的步骤才能完成。</span><span class="sxs-lookup"><span data-stu-id="419e5-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="419e5-163">完成操作的 DevOps 管道中的以下三个部分结果中的步骤。</span><span class="sxs-lookup"><span data-stu-id="419e5-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="419e5-164">授予 Azure DevOps 到 GitHub 存储库的访问权限</span><span class="sxs-lookup"><span data-stu-id="419e5-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="419e5-165">展开**或从外部存储库的代码生成**accordion。</span><span class="sxs-lookup"><span data-stu-id="419e5-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="419e5-166">单击**安装程序生成**按钮：</span><span class="sxs-lookup"><span data-stu-id="419e5-166">Click the **Setup Build** button:</span></span>

    ![安装程序生成按钮](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="419e5-168">选择**GitHub**选项从**选择一个源**部分：</span><span class="sxs-lookup"><span data-stu-id="419e5-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![选择源-GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="419e5-170">授权是必需的 Azure DevOps 可以访问 GitHub 存储库之前。</span><span class="sxs-lookup"><span data-stu-id="419e5-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="419e5-171">输入 *< GitHub_username > GitHub 连接*中**连接名称**文本框中。</span><span class="sxs-lookup"><span data-stu-id="419e5-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="419e5-172">例如：</span><span class="sxs-lookup"><span data-stu-id="419e5-172">For example:</span></span>

    ![GitHub 的连接名称](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="419e5-174">如果你的 GitHub 帐户启用双因素身份验证，则需要个人访问令牌。</span><span class="sxs-lookup"><span data-stu-id="419e5-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="419e5-175">在这种情况下，单击**使用 GitHub 个人访问令牌的授权**链接。</span><span class="sxs-lookup"><span data-stu-id="419e5-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="419e5-176">请参阅[正式 GitHub 个人访问令牌创建说明](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)有关的帮助。</span><span class="sxs-lookup"><span data-stu-id="419e5-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="419e5-177">仅*存储库*所需权限的作用域。</span><span class="sxs-lookup"><span data-stu-id="419e5-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="419e5-178">否则，请单击**使用 OAuth 授权**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="419e5-179">出现提示时，登录到你的 GitHub 帐户。</span><span class="sxs-lookup"><span data-stu-id="419e5-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="419e5-180">然后，选择授权授予对你的 Azure DevOps 组织访问权限。</span><span class="sxs-lookup"><span data-stu-id="419e5-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="419e5-181">如果成功，会创建新的服务终结点。</span><span class="sxs-lookup"><span data-stu-id="419e5-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="419e5-182">单击省略号按钮旁边**存储库**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="419e5-183">选择 *< GitHub_username > / 简单源阅读器*从列表中的存储库。</span><span class="sxs-lookup"><span data-stu-id="419e5-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="419e5-184">单击**选择**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="419e5-185">选择*主*从分支**手动和计划生成的默认分支**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="419e5-186">单击“继续”按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-186">Click the **Continue** button.</span></span> <span data-ttu-id="419e5-187">模板选择页会显示。</span><span class="sxs-lookup"><span data-stu-id="419e5-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="419e5-188">创建生成定义</span><span class="sxs-lookup"><span data-stu-id="419e5-188">Create the build definition</span></span>

1. <span data-ttu-id="419e5-189">从模板选择页中，输入*ASP.NET Core*在搜索框中：</span><span class="sxs-lookup"><span data-stu-id="419e5-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core 模板页上的搜索](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="419e5-191">显示模板搜索结果。</span><span class="sxs-lookup"><span data-stu-id="419e5-191">The template search results appear.</span></span> <span data-ttu-id="419e5-192">将鼠标悬停**ASP.NET Core**模板，然后单击**应用**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="419e5-193">**任务**显示的生成定义选项卡。</span><span class="sxs-lookup"><span data-stu-id="419e5-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="419e5-194">单击“触发器”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="419e5-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="419e5-195">检查**启用持续集成**框。</span><span class="sxs-lookup"><span data-stu-id="419e5-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="419e5-196">下**的分支筛选器**部分中，确认**类型**下拉列表设置为*Include*。</span><span class="sxs-lookup"><span data-stu-id="419e5-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="419e5-197">设置**分支规范**下拉列表*master*。</span><span class="sxs-lookup"><span data-stu-id="419e5-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![启用持续集成设置](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="419e5-199">这些设置会导致要进行任何更改推送到时触发的生成*主*GitHub 存储库分支。</span><span class="sxs-lookup"><span data-stu-id="419e5-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="419e5-200">在中测试持续集成[更改提交到 GitHub 并自动部署到 Azure](#commit-changes-to-github-and-automatically-deploy-to-azure)部分。</span><span class="sxs-lookup"><span data-stu-id="419e5-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="419e5-201">单击**保存并排队**按钮，然后选择**保存**选项：</span><span class="sxs-lookup"><span data-stu-id="419e5-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![保存按钮](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="419e5-203">下面的模式对话框会显示：</span><span class="sxs-lookup"><span data-stu-id="419e5-203">The following modal dialog appears:</span></span>

    ![保存生成定义的模式对话框](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="419e5-205">使用的默认文件夹 *\\*，然后单击**保存**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="419e5-206">创建发布管道</span><span class="sxs-lookup"><span data-stu-id="419e5-206">Create the release pipeline</span></span>

1. <span data-ttu-id="419e5-207">单击**版本**你的团队项目的选项卡。</span><span class="sxs-lookup"><span data-stu-id="419e5-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="419e5-208">单击**新的管道**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-208">Click the **New pipeline** button.</span></span>

    ![版本选项卡的定义新建按钮](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="419e5-210">模板选择窗格会显示。</span><span class="sxs-lookup"><span data-stu-id="419e5-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="419e5-211">从模板选择页中，输入*应用服务*在搜索框中：</span><span class="sxs-lookup"><span data-stu-id="419e5-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![发布管道模板搜索框](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="419e5-213">显示模板搜索结果。</span><span class="sxs-lookup"><span data-stu-id="419e5-213">The template search results appear.</span></span> <span data-ttu-id="419e5-214">将鼠标悬停**Azure 应用服务部署槽**模板，然后单击**应用**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="419e5-215">**管道**发布管道的选项卡将出现。</span><span class="sxs-lookup"><span data-stu-id="419e5-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![发布管道管道选项卡](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="419e5-217">单击**外**按钮**项目**框。</span><span class="sxs-lookup"><span data-stu-id="419e5-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="419e5-218">**添加项目**面板将显示：</span><span class="sxs-lookup"><span data-stu-id="419e5-218">The **Add artifact** panel appears:</span></span>

    ![发布管道-添加项目面板](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="419e5-220">选择**构建**磁贴**源类型**部分。</span><span class="sxs-lookup"><span data-stu-id="419e5-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="419e5-221">此类型，用于对生成定义发布管道的链接。</span><span class="sxs-lookup"><span data-stu-id="419e5-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="419e5-222">选择*MyFirstProject*从**项目**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="419e5-223">选择生成定义名称， *MyFirstProject ASP.NET Core CI*，从**源 （生成定义）** 下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="419e5-224">选择*最新*从**默认版本**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="419e5-225">此选项可用于构建生成的最后一次运行的生成定义的项目。</span><span class="sxs-lookup"><span data-stu-id="419e5-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="419e5-226">替换中的文本**源别名**具有 textbox *Drop*。</span><span class="sxs-lookup"><span data-stu-id="419e5-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="419e5-227">单击“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-227">Click the **Add** button.</span></span> <span data-ttu-id="419e5-228">**项目**部分更新以显示所做的更改。</span><span class="sxs-lookup"><span data-stu-id="419e5-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="419e5-229">单击闪电形图标以启用持续部署：</span><span class="sxs-lookup"><span data-stu-id="419e5-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![发布管道项目的闪电形图标](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="419e5-231">启用此选项，每次新的生成时可执行部署。</span><span class="sxs-lookup"><span data-stu-id="419e5-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="419e5-232">一个**持续部署触发器**面板右侧显示。</span><span class="sxs-lookup"><span data-stu-id="419e5-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="419e5-233">单击切换按钮以启用该功能。</span><span class="sxs-lookup"><span data-stu-id="419e5-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="419e5-234">但并不需要启用**拉取请求触发器**。</span><span class="sxs-lookup"><span data-stu-id="419e5-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="419e5-235">单击**外**下拉列表中**版本分支筛选器**部分。</span><span class="sxs-lookup"><span data-stu-id="419e5-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="419e5-236">选择**生成定义的默认分支**选项。</span><span class="sxs-lookup"><span data-stu-id="419e5-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="419e5-237">此筛选器将导致仅对从 GitHub 存储库的生成触发发布*主*分支。</span><span class="sxs-lookup"><span data-stu-id="419e5-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="419e5-238">单击“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-238">Click the **Save** button.</span></span> <span data-ttu-id="419e5-239">单击**确定**在随后出现的按钮**保存**模式对话框。</span><span class="sxs-lookup"><span data-stu-id="419e5-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="419e5-240">单击**环境 1**框。</span><span class="sxs-lookup"><span data-stu-id="419e5-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="419e5-241">**环境**面板右侧显示。</span><span class="sxs-lookup"><span data-stu-id="419e5-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="419e5-242">更改*环境 1*中的文本**环境名称**的 textbox*生产*。</span><span class="sxs-lookup"><span data-stu-id="419e5-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![发布管道的环境名称文本框](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="419e5-244">单击**1 阶段，2 个任务**中的链接**生产**框：</span><span class="sxs-lookup"><span data-stu-id="419e5-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![发布管道的生产环境 link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="419e5-246">**任务**环境的选项卡将出现。</span><span class="sxs-lookup"><span data-stu-id="419e5-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="419e5-247">单击**将 Azure 应用服务部署槽**任务。</span><span class="sxs-lookup"><span data-stu-id="419e5-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="419e5-248">在右侧面板中显示其设置。</span><span class="sxs-lookup"><span data-stu-id="419e5-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="419e5-249">选择与中的应用服务相关联的 Azure 订阅**Azure 订阅**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="419e5-250">选择后，单击**Authorize**按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="419e5-251">选择*Web 应用*从**应用类型**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="419e5-252">选择*mywebapp / < unique_number / >* 从**应用服务名称**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="419e5-253">选择*AzureTutorial*从**资源组**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="419e5-254">选择*暂存*从**槽**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="419e5-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="419e5-255">单击“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="419e5-256">将鼠标悬停默认发布管道名称。</span><span class="sxs-lookup"><span data-stu-id="419e5-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="419e5-257">单击铅笔图标以对其进行编辑。</span><span class="sxs-lookup"><span data-stu-id="419e5-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="419e5-258">使用*MyFirstProject ASP.NET Core CD*作为名称。</span><span class="sxs-lookup"><span data-stu-id="419e5-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![发布管道名称](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="419e5-260">单击“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="419e5-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="419e5-261">将更改提交到 GitHub 并自动部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="419e5-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="419e5-262">打开*SimpleFeedReader.sln* Visual Studio 中。</span><span class="sxs-lookup"><span data-stu-id="419e5-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="419e5-263">在解决方案资源管理器中打开*Pages\Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="419e5-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="419e5-264">更改`<h2>Simple Feed Reader - V3</h2>`到`<h2>Simple Feed Reader - V4</h2>`。</span><span class="sxs-lookup"><span data-stu-id="419e5-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="419e5-265">按**Ctrl**+**Shift**+**B**生成的应用。</span><span class="sxs-lookup"><span data-stu-id="419e5-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="419e5-266">将文件提交到 GitHub 存储库。</span><span class="sxs-lookup"><span data-stu-id="419e5-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="419e5-267">可以使用两种**更改**Visual Studio 中的页*团队资源管理器*选项卡上，或执行以下命令使用本地计算机的命令行界面：</span><span class="sxs-lookup"><span data-stu-id="419e5-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="419e5-268">将更改推入*主*到分支*原点*远程的 GitHub 存储库：</span><span class="sxs-lookup"><span data-stu-id="419e5-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="419e5-269">GitHub 存储库中将显示提交*主*分支：</span><span class="sxs-lookup"><span data-stu-id="419e5-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![主分支中的 GitHub 提交](media/cicd/github-commit.png)

    <span data-ttu-id="419e5-271">触发生成，因为在生成定义中启用持续集成**触发器**选项卡：</span><span class="sxs-lookup"><span data-stu-id="419e5-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![启用持续集成](media/cicd/enable-ci.png)

1. <span data-ttu-id="419e5-273">导航到**排队**选项卡**Azure 管道** > **生成**Azure DevOps 服务中的页。</span><span class="sxs-lookup"><span data-stu-id="419e5-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="419e5-274">已排队的生成显示分支和提交触发生成：</span><span class="sxs-lookup"><span data-stu-id="419e5-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![排队的生成](media/cicd/build-queued.png)

1. <span data-ttu-id="419e5-276">生成成功后，会发生到 Azure 的部署。</span><span class="sxs-lookup"><span data-stu-id="419e5-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="419e5-277">导航到在浏览器中的应用。</span><span class="sxs-lookup"><span data-stu-id="419e5-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="419e5-278">请注意，显示在标题中的"V4"文本：</span><span class="sxs-lookup"><span data-stu-id="419e5-278">Notice that the "V4" text appears in the heading:</span></span>

    ![更新的应用程序](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="419e5-280">检查 Azure 管道管道</span><span class="sxs-lookup"><span data-stu-id="419e5-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="419e5-281">生成定义</span><span class="sxs-lookup"><span data-stu-id="419e5-281">Build definition</span></span>

<span data-ttu-id="419e5-282">生成定义已创建了同名*MyFirstProject ASP.NET Core CI*。</span><span class="sxs-lookup"><span data-stu-id="419e5-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="419e5-283">完成后，生成过程将产生 *.zip*文件包括要发布的资产。</span><span class="sxs-lookup"><span data-stu-id="419e5-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="419e5-284">发布管道将这些资产部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="419e5-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="419e5-285">生成定义**任务**选项卡列出了正在使用的各个步骤。</span><span class="sxs-lookup"><span data-stu-id="419e5-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="419e5-286">有五个生成任务。</span><span class="sxs-lookup"><span data-stu-id="419e5-286">There are five build tasks.</span></span>

![生成定义任务](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="419e5-288">**还原**&mdash;执行`dotnet restore`命令还原应用程序的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="419e5-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="419e5-289">默认包使用的源是 nuget.org。</span><span class="sxs-lookup"><span data-stu-id="419e5-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="419e5-290">**构建**&mdash;执行`dotnet build --configuration release`命令以编译应用程序的代码。</span><span class="sxs-lookup"><span data-stu-id="419e5-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="419e5-291">这`--configuration`选项用于生成代码，这是适用于部署到生产环境中的优化的版本。</span><span class="sxs-lookup"><span data-stu-id="419e5-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="419e5-292">修改*BuildConfiguration*在生成定义的变量**变量**选项卡上，如果需要调试配置，例如。</span><span class="sxs-lookup"><span data-stu-id="419e5-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="419e5-293">**测试**&mdash;执行`dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>`命令以运行应用的单元测试。</span><span class="sxs-lookup"><span data-stu-id="419e5-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="419e5-294">在 C# 项目匹配的任何执行单元测试的`**/*Tests/*.csproj`glob 模式。</span><span class="sxs-lookup"><span data-stu-id="419e5-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="419e5-295">在保存测试结果 *.trx*文件在指定的位置`--results-directory`选项。</span><span class="sxs-lookup"><span data-stu-id="419e5-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="419e5-296">如果任何测试失败，生成将失败，并且未部署。</span><span class="sxs-lookup"><span data-stu-id="419e5-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="419e5-297">若要验证的单元测试工作，请修改*SimpleFeedReader.Tests\Services\NewsServiceTests.cs*特意中断的测试之一。</span><span class="sxs-lookup"><span data-stu-id="419e5-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="419e5-298">例如，更改`Assert.True(result.Count > 0);`到`Assert.False(result.Count > 0);`中`Returns_News_Stories_Given_Valid_Uri`方法。</span><span class="sxs-lookup"><span data-stu-id="419e5-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="419e5-299">提交并推送到 GitHub 的更改。</span><span class="sxs-lookup"><span data-stu-id="419e5-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="419e5-300">生成被触发，但失败。</span><span class="sxs-lookup"><span data-stu-id="419e5-300">The build is triggered and fails.</span></span> <span data-ttu-id="419e5-301">生成管道状态将变为**失败**。</span><span class="sxs-lookup"><span data-stu-id="419e5-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="419e5-302">再次还原更改、 提交并推送。</span><span class="sxs-lookup"><span data-stu-id="419e5-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="419e5-303">生成成功。</span><span class="sxs-lookup"><span data-stu-id="419e5-303">The build succeeds.</span></span>

1. <span data-ttu-id="419e5-304">**将发布**&mdash;执行`dotnet publish --configuration release --output <local_path_on_build_agent>`命令，生成 *.zip*要部署的项目文件。</span><span class="sxs-lookup"><span data-stu-id="419e5-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="419e5-305">`--output`选项指定的发布位置 *.zip*文件。</span><span class="sxs-lookup"><span data-stu-id="419e5-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="419e5-306">表示通过指定位置[预定义的变量](/azure/devops/pipelines/build/variables)名为`$(build.artifactstagingdirectory)`。</span><span class="sxs-lookup"><span data-stu-id="419e5-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="419e5-307">该变量将扩展到本地路径，如*c:\agent\_work\1\a*，生成代理上。</span><span class="sxs-lookup"><span data-stu-id="419e5-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="419e5-308">**发布项目** &mdash; Publishes *.zip*生成文件**发布**任务。</span><span class="sxs-lookup"><span data-stu-id="419e5-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="419e5-309">该任务接受 *.zip*文件作为参数，这是预定义的变量的位置`$(build.artifactstagingdirectory)`。</span><span class="sxs-lookup"><span data-stu-id="419e5-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="419e5-310">*.Zip*文件发布为一个名为文件夹*drop*。</span><span class="sxs-lookup"><span data-stu-id="419e5-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="419e5-311">单击生成定义**摘要**链接以查看与定义的生成历史记录：</span><span class="sxs-lookup"><span data-stu-id="419e5-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![屏幕截图显示生成定义历史记录](media/cicd/build-definition-summary.png)

<span data-ttu-id="419e5-313">在结果页上，单击与唯一的内部版本号对应的链接：</span><span class="sxs-lookup"><span data-stu-id="419e5-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![屏幕截图显示生成定义摘要页](media/cicd/build-definition-completed.png)

<span data-ttu-id="419e5-315">显示此特定生成的摘要。</span><span class="sxs-lookup"><span data-stu-id="419e5-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="419e5-316">单击**项目**选项卡，并请注意*drop*列出由生成内容生成的文件夹：</span><span class="sxs-lookup"><span data-stu-id="419e5-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![显示生成定义项目的放置文件夹的屏幕截图](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="419e5-318">使用**下载**并**浏览**检查已发布的项目的链接。</span><span class="sxs-lookup"><span data-stu-id="419e5-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="419e5-319">发布管道</span><span class="sxs-lookup"><span data-stu-id="419e5-319">Release pipeline</span></span>

<span data-ttu-id="419e5-320">发布管道已创建了同名*MyFirstProject ASP.NET Core CD*:</span><span class="sxs-lookup"><span data-stu-id="419e5-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![屏幕截图显示发行管道概述](media/cicd/release-definition-overview.png)

<span data-ttu-id="419e5-322">发布管道的两个主要组件是**项目**并**环境**。</span><span class="sxs-lookup"><span data-stu-id="419e5-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="419e5-323">单击框中的**项目**部分会显示以下窗格：</span><span class="sxs-lookup"><span data-stu-id="419e5-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![屏幕截图显示发布管道项目](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="419e5-325">**源 （生成定义）** 值表示此发布管道链接到生成定义。</span><span class="sxs-lookup"><span data-stu-id="419e5-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="419e5-326">*.Zip*成功运行的生成定义所生成文件提供给*生产*环境以部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="419e5-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="419e5-327">单击*1 阶段，2 个任务*中的链接*生产*环境，若要查看发布管道任务：</span><span class="sxs-lookup"><span data-stu-id="419e5-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![屏幕截图显示发布管道任务](media/cicd/release-definition-tasks.png)

<span data-ttu-id="419e5-329">发布管道包括两个任务：*将 Azure 应用服务部署到槽*并*管理 Azure 应用服务-槽交换*。</span><span class="sxs-lookup"><span data-stu-id="419e5-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="419e5-330">单击第一个任务将显示下面的任务配置：</span><span class="sxs-lookup"><span data-stu-id="419e5-330">Clicking the first task reveals the following task configuration:</span></span>

![屏幕截图显示发布管道部署任务](media/cicd/release-definition-task1.png)

<span data-ttu-id="419e5-332">Azure 订阅、 服务类型、 web 应用名称、 资源组和部署槽部署任务中定义。</span><span class="sxs-lookup"><span data-stu-id="419e5-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="419e5-333">**包或文件夹**文本框中保留 *.zip*要提取并部署到的文件路径*暂存*槽*mywebapp\<唯一数目 （_n)\>*  web 应用。</span><span class="sxs-lookup"><span data-stu-id="419e5-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="419e5-334">单击该槽交换任务将显示下面的任务配置：</span><span class="sxs-lookup"><span data-stu-id="419e5-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![屏幕截图显示发布管道槽交换任务](media/cicd/release-definition-task2.png)

<span data-ttu-id="419e5-336">提供订阅、 资源组、 服务类型、 web 应用名称和部署槽详细信息。</span><span class="sxs-lookup"><span data-stu-id="419e5-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="419e5-337">**与生成交换**选中复选框。</span><span class="sxs-lookup"><span data-stu-id="419e5-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="419e5-338">因此，将位部署到*暂存*槽交换到生产环境。</span><span class="sxs-lookup"><span data-stu-id="419e5-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="419e5-339">其他阅读材料</span><span class="sxs-lookup"><span data-stu-id="419e5-339">Additional reading</span></span>

* [<span data-ttu-id="419e5-340">使用 Azure Pipelines 创建你的第一个管道</span><span class="sxs-lookup"><span data-stu-id="419e5-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="419e5-341">生成和.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="419e5-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="419e5-342">部署 web 应用与 Azure 管道</span><span class="sxs-lookup"><span data-stu-id="419e5-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
