---
title: 持续集成和持续部署 - 通过 ASP.NET Core 和 Azure 实现 DevOps
author: CamSoper
description: 在 DevOps 中使用 ASP.NET Core 和 Azure 进行持续集成和持续部署
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: azure/devops/cicd
ms.openlocfilehash: eddd7034bf1860fb35cf00eefb7a11a408869700
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052638"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="7441d-103">持续集成和持续部署</span><span class="sxs-lookup"><span data-stu-id="7441d-103">Continuous integration and deployment</span></span>

<span data-ttu-id="7441d-104">在上一章中，你为简单源读取器应用创建了一个本地 Git 存储库。</span><span class="sxs-lookup"><span data-stu-id="7441d-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="7441d-105">在本章中，你将把代码发布到 GitHub 存储库，并使用 Azure Pipelines 构建 Azure DevOps Services 管道。</span><span class="sxs-lookup"><span data-stu-id="7441d-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="7441d-106">管道支持应用的持续生成和持续部署。</span><span class="sxs-lookup"><span data-stu-id="7441d-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="7441d-107">对 GitHub 存储库的任何提交都会触发对 Azure Web 应用过渡槽的生成和部署。</span><span class="sxs-lookup"><span data-stu-id="7441d-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="7441d-108">在本节中，你将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="7441d-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="7441d-109">将应用的代码发布到 GitHub</span><span class="sxs-lookup"><span data-stu-id="7441d-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="7441d-110">断开本地 Git 部署连接</span><span class="sxs-lookup"><span data-stu-id="7441d-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="7441d-111">创建一个 Azure DevOps 组织</span><span class="sxs-lookup"><span data-stu-id="7441d-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="7441d-112">在 Azure DevOps Services 中创建团队项目</span><span class="sxs-lookup"><span data-stu-id="7441d-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="7441d-113">创建生成定义</span><span class="sxs-lookup"><span data-stu-id="7441d-113">Create a build definition</span></span>
* <span data-ttu-id="7441d-114">创建发布管道</span><span class="sxs-lookup"><span data-stu-id="7441d-114">Create a release pipeline</span></span>
* <span data-ttu-id="7441d-115">提交对 GitHub 所做的更改并将其自动部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="7441d-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="7441d-116">检查 Azure Pipelines 管道</span><span class="sxs-lookup"><span data-stu-id="7441d-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="7441d-117">将应用的代码发布到 GitHub</span><span class="sxs-lookup"><span data-stu-id="7441d-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="7441d-118">打开浏览器窗口并导航到 `https://github.com`。</span><span class="sxs-lookup"><span data-stu-id="7441d-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="7441d-119">单击标头中的 + 下拉列表，然后选择“新建存储库” ：</span><span class="sxs-lookup"><span data-stu-id="7441d-119">Click the **+** drop-down in the header, and select **New repository** :</span></span>

    ![GitHub“新建存储库”选项](media/cicd/github-new-repo.png)

1. <span data-ttu-id="7441d-121">在“所有者”下拉列表中选择你的帐户，然后在“存储库名称”文本框中输入“简单源读取器” 。</span><span class="sxs-lookup"><span data-stu-id="7441d-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="7441d-122">单击“创建存储库”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="7441d-123">打开本地计算机的命令行界面。</span><span class="sxs-lookup"><span data-stu-id="7441d-123">Open your local machine's command shell.</span></span> <span data-ttu-id="7441d-124">导航到存储简单源读取器 Git 存储库的目录。</span><span class="sxs-lookup"><span data-stu-id="7441d-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="7441d-125">将现有的源远程库重命名为“上游” 。</span><span class="sxs-lookup"><span data-stu-id="7441d-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="7441d-126">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7441d-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="7441d-127">添加指向一个 GitHub 上存储库的副本的新源远程库。</span><span class="sxs-lookup"><span data-stu-id="7441d-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="7441d-128">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7441d-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="7441d-129">将本地 Git 存储库发布到新创建的 GitHub 存储库。</span><span class="sxs-lookup"><span data-stu-id="7441d-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="7441d-130">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7441d-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="7441d-131">打开浏览器窗口并导航到 `https://github.com/<GitHub_username>/simple-feed-reader/`。</span><span class="sxs-lookup"><span data-stu-id="7441d-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="7441d-132">验证你的代码是否显示在 GitHub 存储库中。</span><span class="sxs-lookup"><span data-stu-id="7441d-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="7441d-133">断开本地 Git 部署连接</span><span class="sxs-lookup"><span data-stu-id="7441d-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="7441d-134">通过以下步骤删除本地 Git 部署。</span><span class="sxs-lookup"><span data-stu-id="7441d-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="7441d-135">Azure Pipelines（一种 Azure DevOps 服务）既替代又增强了该功能。</span><span class="sxs-lookup"><span data-stu-id="7441d-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="7441d-136">打开 [Azure 门户](https://portal.azure.com/)，导航到暂存 (mywebapp\<unique_number\>/staging) Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7441d-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="7441d-137">通过在门户的搜索框中输入“暂存”，可以快速找到该 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="7441d-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![暂存 Web 应用搜索术语](media/cicd/portal-search-box.png)

1. <span data-ttu-id="7441d-139">单击“部署中心”。</span><span class="sxs-lookup"><span data-stu-id="7441d-139">Click **Deployment Center**.</span></span> <span data-ttu-id="7441d-140">此时将显示一个新面板。</span><span class="sxs-lookup"><span data-stu-id="7441d-140">A new panel appears.</span></span> <span data-ttu-id="7441d-141">单击“断开连接”以删除上一章中添加的本地 Git 源代码管理配置。</span><span class="sxs-lookup"><span data-stu-id="7441d-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="7441d-142">单击“是”按钮，确认删除操作。</span><span class="sxs-lookup"><span data-stu-id="7441d-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="7441d-143">导航到“mywebapp<unique_number>”应用服务。</span><span class="sxs-lookup"><span data-stu-id="7441d-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="7441d-144">在此提醒，可使用门户的搜索框快速找到该应用服务。</span><span class="sxs-lookup"><span data-stu-id="7441d-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="7441d-145">单击“部署中心”。</span><span class="sxs-lookup"><span data-stu-id="7441d-145">Click **Deployment Center**.</span></span> <span data-ttu-id="7441d-146">此时将显示一个新面板。</span><span class="sxs-lookup"><span data-stu-id="7441d-146">A new panel appears.</span></span> <span data-ttu-id="7441d-147">单击“断开连接”以删除上一章中添加的本地 Git 源代码管理配置。</span><span class="sxs-lookup"><span data-stu-id="7441d-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="7441d-148">单击“是”按钮，确认删除操作。</span><span class="sxs-lookup"><span data-stu-id="7441d-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="7441d-149">创建一个 Azure DevOps 组织</span><span class="sxs-lookup"><span data-stu-id="7441d-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="7441d-150">打开浏览器，并导航到 [Azure DevOps 组织创建页](https://go.microsoft.com/fwlink/?LinkId=307137)。</span><span class="sxs-lookup"><span data-stu-id="7441d-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="7441d-151">在“选取一个便于记忆的名称”文本框中键入一个唯一名称，形成用于访问 Azure DevOps 组织的 URL。</span><span class="sxs-lookup"><span data-stu-id="7441d-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="7441d-152">选择“Git”单选按钮，因为代码托管在 GitHub 存储库中。</span><span class="sxs-lookup"><span data-stu-id="7441d-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="7441d-153">单击“继续”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-153">Click the **Continue** button.</span></span> <span data-ttu-id="7441d-154">片刻后，一个帐户以及一个名为 MyFirstProject 的团队项目随之创建。</span><span class="sxs-lookup"><span data-stu-id="7441d-154">After a short wait, an account and a team project, named *MyFirstProject* , are created.</span></span>

    ![Azure DevOps 组织创建页](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="7441d-156">打开确认电子邮件，该邮件指示 Azure DevOps 组织和项目已准备就绪，可供使用。</span><span class="sxs-lookup"><span data-stu-id="7441d-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="7441d-157">单击“启动项目”按钮：</span><span class="sxs-lookup"><span data-stu-id="7441d-157">Click the **Start your project** button:</span></span>

    ![“启动项目”按钮](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="7441d-159">浏览器将打开 \<account_name\>.visualstudio.com。</span><span class="sxs-lookup"><span data-stu-id="7441d-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="7441d-160">单击“MyFirstProject”链接，开始配置项目的 DevOps 管道。</span><span class="sxs-lookup"><span data-stu-id="7441d-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="7441d-161">配置 Azure Pipelines 管道</span><span class="sxs-lookup"><span data-stu-id="7441d-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="7441d-162">需要完成三个不同的步骤。</span><span class="sxs-lookup"><span data-stu-id="7441d-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="7441d-163">完成以下三节中的步骤将生成一个可操作的 DevOps 管道。</span><span class="sxs-lookup"><span data-stu-id="7441d-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="7441d-164">授予 Azure DevOps 对 GitHub 存储库的访问权限</span><span class="sxs-lookup"><span data-stu-id="7441d-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="7441d-165">展开“或从外部存储库生成代码”可折叠项。</span><span class="sxs-lookup"><span data-stu-id="7441d-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="7441d-166">单击“设置生成”按钮：</span><span class="sxs-lookup"><span data-stu-id="7441d-166">Click the **Setup Build** button:</span></span>

    ![“设置生成”按钮](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="7441d-168">从“选择源”部分中选择“GitHub”选项 ：</span><span class="sxs-lookup"><span data-stu-id="7441d-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![选择源 - GitHub](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="7441d-170">Azure DevOps 需要经过授权才可访问 GitHub 存储库。</span><span class="sxs-lookup"><span data-stu-id="7441d-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="7441d-171">在“连接名称”文本框中输入“<GitHub_username> GitHub 连接”。</span><span class="sxs-lookup"><span data-stu-id="7441d-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="7441d-172">例如：</span><span class="sxs-lookup"><span data-stu-id="7441d-172">For example:</span></span>

    ![GitHub 连接名称](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="7441d-174">如果对 GitHub 帐户启用了双因素身份验证，则需要个人访问令牌。</span><span class="sxs-lookup"><span data-stu-id="7441d-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="7441d-175">在这种情况下，单击“使用 GitHub 个人访问令牌授权”链接。</span><span class="sxs-lookup"><span data-stu-id="7441d-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="7441d-176">有关帮助，请参阅[官方 GitHub 个人访问令牌创建说明](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。</span><span class="sxs-lookup"><span data-stu-id="7441d-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="7441d-177">只需要存储库范围内的权限。</span><span class="sxs-lookup"><span data-stu-id="7441d-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="7441d-178">否则，单击“使用 OAuth 授权”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="7441d-179">出现提示时，请登录到你的 GitHub 帐户。</span><span class="sxs-lookup"><span data-stu-id="7441d-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="7441d-180">然后，选择“授权”，授予对 Azure DevOps 组织的访问权限。</span><span class="sxs-lookup"><span data-stu-id="7441d-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="7441d-181">如果成功，新的服务终结点随之创建。</span><span class="sxs-lookup"><span data-stu-id="7441d-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="7441d-182">单击“存储库”按钮旁边的省略号按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="7441d-183">从列表中选择“<GitHub_username>/simple-feed-reader”存储库。</span><span class="sxs-lookup"><span data-stu-id="7441d-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="7441d-184">单击“选择”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="7441d-185">从“用于手动生成和计划生成的默认分支”下拉列表中选择主分支。</span><span class="sxs-lookup"><span data-stu-id="7441d-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="7441d-186">单击“继续”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-186">Click the **Continue** button.</span></span> <span data-ttu-id="7441d-187">此时将显示模板选择页。</span><span class="sxs-lookup"><span data-stu-id="7441d-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="7441d-188">创建生成定义</span><span class="sxs-lookup"><span data-stu-id="7441d-188">Create the build definition</span></span>

1. <span data-ttu-id="7441d-189">在模板选择页的“搜索”框中输入 ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="7441d-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![模板页上的 ASP.NET Core 搜索](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="7441d-191">模板搜索结果随即显示。</span><span class="sxs-lookup"><span data-stu-id="7441d-191">The template search results appear.</span></span> <span data-ttu-id="7441d-192">将鼠标悬停在“ASP.NET Core”模板上，然后单击“应用”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7441d-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="7441d-193">生成定义的“任务”选项卡随即出现。</span><span class="sxs-lookup"><span data-stu-id="7441d-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="7441d-194">单击“触发器”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="7441d-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="7441d-195">单击“启用持续集成”框。</span><span class="sxs-lookup"><span data-stu-id="7441d-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="7441d-196">在“分支筛选器”部分下，确认“类型”下拉列表设置为“包括” 。</span><span class="sxs-lookup"><span data-stu-id="7441d-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="7441d-197">将“分支规范”下拉列表设置为“主”。</span><span class="sxs-lookup"><span data-stu-id="7441d-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![启用持续集成设置](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="7441d-199">这些设置会在将任何更改推送到 GitHub 存储库的主分支时触发生成。</span><span class="sxs-lookup"><span data-stu-id="7441d-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="7441d-200">持续集成在[提交对 GitHub 的更改并自动部署到 Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) 一节中进行测试。</span><span class="sxs-lookup"><span data-stu-id="7441d-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="7441d-201">单击“保存并排队”按钮，然后选择“保存”选项 ：</span><span class="sxs-lookup"><span data-stu-id="7441d-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![“保存”按钮](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="7441d-203">以下模式对话框随即显示：</span><span class="sxs-lookup"><span data-stu-id="7441d-203">The following modal dialog appears:</span></span>

    ![保存生成定义 - 模式对话框](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="7441d-205">使用 \\ 的默认文件夹，然后单击“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-205">Use the default folder of *\\* , and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="7441d-206">创建发布管道</span><span class="sxs-lookup"><span data-stu-id="7441d-206">Create the release pipeline</span></span>

1. <span data-ttu-id="7441d-207">单击团队项目的“发布”选项卡。</span><span class="sxs-lookup"><span data-stu-id="7441d-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="7441d-208">单击“新建管道”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-208">Click the **New pipeline** button.</span></span>

    ![“发布”选项卡 -“新建”定义按钮](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="7441d-210">此时将显示模板选择窗格。</span><span class="sxs-lookup"><span data-stu-id="7441d-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="7441d-211">在模板选择页的搜索框中输入应用服务：</span><span class="sxs-lookup"><span data-stu-id="7441d-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![发布管道模板搜索框](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="7441d-213">模板搜索结果随即显示。</span><span class="sxs-lookup"><span data-stu-id="7441d-213">The template search results appear.</span></span> <span data-ttu-id="7441d-214">将鼠标悬停在“Azure 应用服务的槽部署”模板上，然后单击“应用”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7441d-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="7441d-215">发布管道的“管道”选项卡出现。</span><span class="sxs-lookup"><span data-stu-id="7441d-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![发布管道的“管道”选项卡](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="7441d-217">单击“项目”框中的“添加”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7441d-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="7441d-218">此时将显示“添加项目”面板：</span><span class="sxs-lookup"><span data-stu-id="7441d-218">The **Add artifact** panel appears:</span></span>

    ![发布管道 - 添加项目面板](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="7441d-220">从“源类型”部分选择“生成”磁贴 。</span><span class="sxs-lookup"><span data-stu-id="7441d-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="7441d-221">此类型允许将发布管道链接到生成定义。</span><span class="sxs-lookup"><span data-stu-id="7441d-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="7441d-222">从“项目”下拉列表中选择“MyFirstProject”。</span><span class="sxs-lookup"><span data-stu-id="7441d-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="7441d-223">从“源(生成定义)”下拉列表中选择生成定义名称“MyFirstProject-ASP.NET Core-CI”。</span><span class="sxs-lookup"><span data-stu-id="7441d-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI* , from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="7441d-224">从“默认版本”下拉列表中选择“最新”。</span><span class="sxs-lookup"><span data-stu-id="7441d-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="7441d-225">此选项生成由最新运行的生成定义生成的项目。</span><span class="sxs-lookup"><span data-stu-id="7441d-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="7441d-226">使用“Drop”替换“源别名”文本框中的文本。</span><span class="sxs-lookup"><span data-stu-id="7441d-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="7441d-227">单击“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-227">Click the **Add** button.</span></span> <span data-ttu-id="7441d-228">“项目”部分将更新以显示更改。</span><span class="sxs-lookup"><span data-stu-id="7441d-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="7441d-229">单击闪电图标以启用持续部署：</span><span class="sxs-lookup"><span data-stu-id="7441d-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![发布管道的“项目”- 闪电符号图标](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="7441d-231">启用此选项后，每次有新生成可用时都会进行部署。</span><span class="sxs-lookup"><span data-stu-id="7441d-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="7441d-232">右侧随即出现“持续部署触发器”面板。</span><span class="sxs-lookup"><span data-stu-id="7441d-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="7441d-233">单击“切换”按钮以启用该功能。</span><span class="sxs-lookup"><span data-stu-id="7441d-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="7441d-234">无需启用“拉取请求触发器”。</span><span class="sxs-lookup"><span data-stu-id="7441d-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="7441d-235">单击“生成分支筛选器”部分中的“添加”下拉列表 。</span><span class="sxs-lookup"><span data-stu-id="7441d-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="7441d-236">选择“生成定义的默认分支”选项。</span><span class="sxs-lookup"><span data-stu-id="7441d-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="7441d-237">此筛选器使得发布仅针对来自 GitHub 存储库的主分支的生成而触发。</span><span class="sxs-lookup"><span data-stu-id="7441d-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="7441d-238">单击“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-238">Click the **Save** button.</span></span> <span data-ttu-id="7441d-239">在生成的“保存”模式对话框中，单击“确定”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7441d-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="7441d-240">单击“环境 1”框。</span><span class="sxs-lookup"><span data-stu-id="7441d-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="7441d-241">右侧随即出现“环境”面板。</span><span class="sxs-lookup"><span data-stu-id="7441d-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="7441d-242">将“环境名称”文本框中的“环境 1”文本更改为“生产” 。</span><span class="sxs-lookup"><span data-stu-id="7441d-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![发布管道 -“环境名称”文本框](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="7441d-244">单击“生产”框中的“1 个阶段、2 个任务”链接 ：</span><span class="sxs-lookup"><span data-stu-id="7441d-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![发布管道 - 生产环境链接图像 (.png)](media/cicd/vsts-production-link.png)

    <span data-ttu-id="7441d-246">此时将显示环境的“任务”选项卡。</span><span class="sxs-lookup"><span data-stu-id="7441d-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="7441d-247">单击“将 Azure 应用服务部署到槽”任务。</span><span class="sxs-lookup"><span data-stu-id="7441d-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="7441d-248">其设置显示在右侧的面板中。</span><span class="sxs-lookup"><span data-stu-id="7441d-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="7441d-249">从“Azure 订阅”下拉列表中选择与应用服务关联的 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="7441d-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="7441d-250">选择后，单击“授权”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="7441d-251">从“应用类型”下拉菜单中选择“Web 应用”。</span><span class="sxs-lookup"><span data-stu-id="7441d-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="7441d-252">从“应用服务名称”下拉列表中选择“mywebapp/<unique_number/>”。</span><span class="sxs-lookup"><span data-stu-id="7441d-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="7441d-253">从“资源组”下拉列表中选择“AzureTutorial”。</span><span class="sxs-lookup"><span data-stu-id="7441d-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="7441d-254">从“槽”下拉列表中选择“暂存”。</span><span class="sxs-lookup"><span data-stu-id="7441d-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="7441d-255">单击“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="7441d-256">将鼠标悬停在默认的发布管道名称上。</span><span class="sxs-lookup"><span data-stu-id="7441d-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="7441d-257">单击铅笔图标进行编辑。</span><span class="sxs-lookup"><span data-stu-id="7441d-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="7441d-258">使用“MyFirstProject-ASP.NET Core-CD”作为名称。</span><span class="sxs-lookup"><span data-stu-id="7441d-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![发布管道名称](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="7441d-260">单击“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="7441d-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="7441d-261">提交对 GitHub 所做的更改并将其自动部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="7441d-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="7441d-262">在 Visual Studio 中打开“SimpleFeedReader.sln”。</span><span class="sxs-lookup"><span data-stu-id="7441d-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="7441d-263">在解决方案资源管理器中，打开 Pages\Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="7441d-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="7441d-264">将 `<h2>Simple Feed Reader - V3</h2>` 更改为 `<h2>Simple Feed Reader - V4</h2>`。</span><span class="sxs-lookup"><span data-stu-id="7441d-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="7441d-265">按 Ctrl+Shift+B 生成应用  。</span><span class="sxs-lookup"><span data-stu-id="7441d-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="7441d-266">将该文件提交到 GitHub 存储库。</span><span class="sxs-lookup"><span data-stu-id="7441d-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="7441d-267">使用 Visual Studio“团队资源管理器”选项卡中的“更改”页，或者使用本地计算机的命令行界面执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7441d-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="7441d-268">将主分支中的更改推送到 GitHub 存储库的源远程库 ：</span><span class="sxs-lookup"><span data-stu-id="7441d-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="7441d-269">提交出现在 GitHub 存储库的主分支中：</span><span class="sxs-lookup"><span data-stu-id="7441d-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![主分支中的 GitHub 提交](media/cicd/github-commit.png)

    <span data-ttu-id="7441d-271">生成被触发，因为在生成定义的“触发器”选项卡中启用了持续集成：</span><span class="sxs-lookup"><span data-stu-id="7441d-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![启用持续集成](media/cicd/enable-ci.png)

1. <span data-ttu-id="7441d-273">导航到 Azure DevOps Services 中“Azure Pipelines” > “生成”页面的“已排队”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="7441d-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="7441d-274">队列生成显示触发生成的分支和提交：</span><span class="sxs-lookup"><span data-stu-id="7441d-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![已排队的生成](media/cicd/build-queued.png)

1. <span data-ttu-id="7441d-276">生成成功后便会部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="7441d-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="7441d-277">在浏览器中导航到应用。</span><span class="sxs-lookup"><span data-stu-id="7441d-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="7441d-278">请注意，“V4”文本显示在标题中：</span><span class="sxs-lookup"><span data-stu-id="7441d-278">Notice that the "V4" text appears in the heading:</span></span>

    ![已更新应用](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="7441d-280">检查 Azure Pipelines 管道</span><span class="sxs-lookup"><span data-stu-id="7441d-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="7441d-281">生成定义</span><span class="sxs-lookup"><span data-stu-id="7441d-281">Build definition</span></span>

<span data-ttu-id="7441d-282">已创建名为“MyFirstProject-ASP.NET Core-CI”的生成定义。</span><span class="sxs-lookup"><span data-stu-id="7441d-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="7441d-283">完成后，该生成会产生一个包含要发布的资产的 .zip文件。</span><span class="sxs-lookup"><span data-stu-id="7441d-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="7441d-284">发布管道将这些资产部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="7441d-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="7441d-285">生成定义的“任务”选项卡列出了所使用的各个步骤。</span><span class="sxs-lookup"><span data-stu-id="7441d-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="7441d-286">有五个生成任务。</span><span class="sxs-lookup"><span data-stu-id="7441d-286">There are five build tasks.</span></span>

![生成定义任务](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="7441d-288">还原 &mdash; 执行 `dotnet restore` 命令，还原应用的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="7441d-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="7441d-289">使用的默认包源为 nuget.org。</span><span class="sxs-lookup"><span data-stu-id="7441d-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="7441d-290">生成 &mdash; 执行 `dotnet build --configuration release` 命令，编译应用的代码。</span><span class="sxs-lookup"><span data-stu-id="7441d-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="7441d-291">此 `--configuration` 选项用于生成代码的优化版本，该版本适合部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="7441d-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="7441d-292">例如，如果需要调试配置，请修改生成定义的“变量”选项卡上的“BuildConfiguration”变量。</span><span class="sxs-lookup"><span data-stu-id="7441d-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="7441d-293">测试 &mdash; 执行 `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 命令，运行应用的单元测试。</span><span class="sxs-lookup"><span data-stu-id="7441d-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="7441d-294">单元测试在与 `**/*Tests/*.csproj` glob 模式匹配的任何 C# 项目中执行。</span><span class="sxs-lookup"><span data-stu-id="7441d-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="7441d-295">测试结果保存在位于 `--results-directory` 选项指定的位置的 .trx 文件中。</span><span class="sxs-lookup"><span data-stu-id="7441d-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="7441d-296">如果任何测试失败，则生成将失败且不会部署。</span><span class="sxs-lookup"><span data-stu-id="7441d-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="7441d-297">若要验证单元测试是否运作正常，请修改 SimpleFeedReader.Tests\Services\NewsServiceTests.cs，有意地中断其中一个测试。</span><span class="sxs-lookup"><span data-stu-id="7441d-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="7441d-298">例如，在 `Returns_News_Stories_Given_Valid_Uri` 方法中，将 `Assert.True(result.Count > 0);` 更改为 `Assert.False(result.Count > 0);`。</span><span class="sxs-lookup"><span data-stu-id="7441d-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="7441d-299">提交更改并将其推送到 GitHub。</span><span class="sxs-lookup"><span data-stu-id="7441d-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="7441d-300">生成已触发并失败。</span><span class="sxs-lookup"><span data-stu-id="7441d-300">The build is triggered and fails.</span></span> <span data-ttu-id="7441d-301">生成管道状态更改为“失败”。</span><span class="sxs-lookup"><span data-stu-id="7441d-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="7441d-302">还原更改、提交并再次推送。</span><span class="sxs-lookup"><span data-stu-id="7441d-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="7441d-303">生成成功。</span><span class="sxs-lookup"><span data-stu-id="7441d-303">The build succeeds.</span></span>

1. <span data-ttu-id="7441d-304">发布&mdash; 执行 `dotnet publish --configuration release --output <local_path_on_build_agent>` 命令，生成包含要部署的项目的 .zip 文件。</span><span class="sxs-lookup"><span data-stu-id="7441d-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="7441d-305">`--output` 选项指定 .zip 文件的发布位置。</span><span class="sxs-lookup"><span data-stu-id="7441d-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="7441d-306">通过传递名为 `$(build.artifactstagingdirectory)` 的[预定义变量](/azure/devops/pipelines/build/variables)来指定该位置。</span><span class="sxs-lookup"><span data-stu-id="7441d-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="7441d-307">该变量扩展到生成代理上的本地路径，如“c:\agent\_work\1\a”。</span><span class="sxs-lookup"><span data-stu-id="7441d-307">That variable expands to a local path, such as *c:\agent\_work\1\a* , on the build agent.</span></span>
1. <span data-ttu-id="7441d-308">发布项目 &mdash; 发布由“发布”任务生成的 .zip 文件 。</span><span class="sxs-lookup"><span data-stu-id="7441d-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="7441d-309">任务接受 .zip 文件位置作为参数，这是预定义的变量 `$(build.artifactstagingdirectory)`。</span><span class="sxs-lookup"><span data-stu-id="7441d-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="7441d-310">.zip 文件作为名为“drop”的文件夹发布 。</span><span class="sxs-lookup"><span data-stu-id="7441d-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="7441d-311">单击生成定义的“摘要”链接，查看具有以下定义的生成的历史记录：</span><span class="sxs-lookup"><span data-stu-id="7441d-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![显示生成定义历史记录的屏幕截图](media/cicd/build-definition-summary.png)

<span data-ttu-id="7441d-313">在生成的页面上，单击与唯一生成号对应的链接：</span><span class="sxs-lookup"><span data-stu-id="7441d-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![显示生成定义摘要页的屏幕截图](media/cicd/build-definition-completed.png)

<span data-ttu-id="7441d-315">随即显示此特定生成的摘要。</span><span class="sxs-lookup"><span data-stu-id="7441d-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="7441d-316">单击“项目”选项卡，便可注意到该生成产生的“drop”文件夹已列出：</span><span class="sxs-lookup"><span data-stu-id="7441d-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![显示生成定义项目 - drop 文件夹的屏幕截图](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="7441d-318">使用“下载”和“浏览”链接检查已发布的项目 。</span><span class="sxs-lookup"><span data-stu-id="7441d-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="7441d-319">发布管道</span><span class="sxs-lookup"><span data-stu-id="7441d-319">Release pipeline</span></span>

<span data-ttu-id="7441d-320">已创建名为“MyFirstProject-ASP.NET Core-CD”的发布管道：</span><span class="sxs-lookup"><span data-stu-id="7441d-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD* :</span></span>

![显示发布管道概述的屏幕截图](media/cicd/release-definition-overview.png)

<span data-ttu-id="7441d-322">发布管道的两个主要组件是“项目”和“环境” 。</span><span class="sxs-lookup"><span data-stu-id="7441d-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="7441d-323">单击“项目”部分中的框将显示以下面板：</span><span class="sxs-lookup"><span data-stu-id="7441d-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![显示发布管道项目的屏幕截图](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="7441d-325">“源(生成定义)”值表示此发布管道链接到的生成定义。</span><span class="sxs-lookup"><span data-stu-id="7441d-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="7441d-326">成功运行生成定义所生成的 .zip 文件将提供给“生产”环境以部署到 Azure 。</span><span class="sxs-lookup"><span data-stu-id="7441d-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="7441d-327">单击“生产”环境框中的“1 个阶段、2 个任务”链接，查看发布管道任务 ：</span><span class="sxs-lookup"><span data-stu-id="7441d-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![显示发布管道任务的屏幕截图](media/cicd/release-definition-tasks.png)

<span data-ttu-id="7441d-329">发布管道包含两个任务：“将 Azure 应用服务部署到槽”和“管理 Azure 应用服务 - 槽交换” 。</span><span class="sxs-lookup"><span data-stu-id="7441d-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="7441d-330">单击第一个任务将显示以下任务配置：</span><span class="sxs-lookup"><span data-stu-id="7441d-330">Clicking the first task reveals the following task configuration:</span></span>

![显示发布管道部署任务的屏幕截图](media/cicd/release-definition-task1.png)

<span data-ttu-id="7441d-332">Azure 订阅、服务类型、Web 应用名称、资源组和部署槽位在部署任务中定义。</span><span class="sxs-lookup"><span data-stu-id="7441d-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="7441d-333">“包或文件夹”文本框保存要提取并部署到 mywebapp\<unique_number\> Web 应用“暂存”槽的 .zip 文件路径。  </span><span class="sxs-lookup"><span data-stu-id="7441d-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="7441d-334">单击槽交换任务将显示以下任务配置：</span><span class="sxs-lookup"><span data-stu-id="7441d-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![显示发布管道槽交换任务的屏幕截图](media/cicd/release-definition-task2.png)

<span data-ttu-id="7441d-336">订阅、资源组、服务类型、Web 应用名称和部署槽位的详细信息已提供。</span><span class="sxs-lookup"><span data-stu-id="7441d-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="7441d-337">“与生成交换”复选框已选中。</span><span class="sxs-lookup"><span data-stu-id="7441d-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="7441d-338">因此，部署到“暂存”槽的位将交换到生产环境中。</span><span class="sxs-lookup"><span data-stu-id="7441d-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="7441d-339">其他阅读材料</span><span class="sxs-lookup"><span data-stu-id="7441d-339">Additional reading</span></span>

* [<span data-ttu-id="7441d-340">使用 Azure Pipelines 创建你的第一个管道</span><span class="sxs-lookup"><span data-stu-id="7441d-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="7441d-341">生成和 .NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="7441d-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="7441d-342">使用 Azure Pipelines 部署 Web 应用</span><span class="sxs-lookup"><span data-stu-id="7441d-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
