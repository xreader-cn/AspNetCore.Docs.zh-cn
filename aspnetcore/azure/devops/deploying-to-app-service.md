---
title: 将应用部署到应用服务-使用 ASP.NET Core 和 Azure 进行开发运营
author: CamSoper
description: 将 ASP.NET Core 应用部署到 Azure 应用服务中，使用 ASP.NET Core 和 Azure 进行开发运营的第一步。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: e09d03f1d30f128b1db1588aa92b28ec3e4ae626
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264387"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="dcf0f-103">将应用部署到应用服务</span><span class="sxs-lookup"><span data-stu-id="dcf0f-103">Deploy an app to App Service</span></span>

<span data-ttu-id="dcf0f-104">[Azure 应用服务](/azure/app-service/)是 Azure 的 web 托管平台。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="dcf0f-105">手动或通过自动化过程，可以完成 web 应用部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="dcf0f-106">指南的本部分讨论可以手动或使用命令行中，脚本触发或触发手动使用 Visual Studio 的部署方法。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="dcf0f-107">在本部分中，将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="dcf0f-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="dcf0f-108">下载并生成示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-108">Download and build the sample app.</span></span>
* <span data-ttu-id="dcf0f-109">创建使用 Azure Cloud Shell 的 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="dcf0f-110">将示例应用程序部署到 Azure 中使用 Git。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="dcf0f-111">将更改部署到使用 Visual Studio 的应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="dcf0f-112">将过渡槽添加到 web 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="dcf0f-113">将更新部署到过渡槽。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="dcf0f-114">交换过渡和生产槽。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="dcf0f-115">下载并测试应用程序</span><span class="sxs-lookup"><span data-stu-id="dcf0f-115">Download and test the app</span></span>

<span data-ttu-id="dcf0f-116">本指南中使用的应用是一个预构建的 ASP.NET Core 应用，[简单馈送读取器](https://github.com/Azure-Samples/simple-feed-reader/)。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="dcf0f-117">它是使用 Razor 页面应用`Microsoft.SyndicationFeed.ReaderWriter`API 以检索 RSS/Atom 馈送，并在列表中显示的新闻项。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="dcf0f-118">随意查看代码，但请务必了解，没有什么特别此应用之处。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="dcf0f-119">正是出于演示目的只是简单的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="dcf0f-120">从命令行界面，下载的代码、 生成项目时，和运行它，如下所示。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="dcf0f-121">*注意：Linux/macOS 用户应更改相应的路径，例如，使用正斜杠 (`/`) 而不是反斜杠 (`\`)。*</span><span class="sxs-lookup"><span data-stu-id="dcf0f-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="dcf0f-122">代码克隆到本地计算机上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="dcf0f-123">更改到您的工作文件夹*简单源阅读器*已创建的文件夹。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="dcf0f-124">还原包，并生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-124">Restore the packages, and build the solution.</span></span>

    ```console
    dotnet build
    ```

4. <span data-ttu-id="dcf0f-125">运行应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-125">Run the app.</span></span>

    ```console
    dotnet run
    ```

    ![Dotnet run 命令成功](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="dcf0f-127">打开浏览器并导航到`http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="dcf0f-128">应用程序，可键入或粘贴联合源 URL，并查看新闻项的列表。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![应用程序显示 RSS 源的内容](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="dcf0f-130">感到满意后应用程序是否正常工作，通过按关闭它**Ctrl**+**C**命令行界面中。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="dcf0f-131">创建 Azure 应用服务 Web 应用</span><span class="sxs-lookup"><span data-stu-id="dcf0f-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="dcf0f-132">若要部署应用程序，你将需要创建应用服务[Web 应用](/azure/app-service/app-service-web-overview)。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="dcf0f-133">创建后的 Web 应用，你将部署到它从使用 Git 在本地计算机。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="dcf0f-134">登录到[Azure Cloud Shell](https://shell.azure.com/bash)。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="dcf0f-135">注意:在登录时第一次，Cloud Shell 会提示创建配置文件的存储帐户。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="dcf0f-136">接受默认值或提供唯一的名称。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="dcf0f-137">对于以下步骤使用 Cloud Shell。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="dcf0f-138">a.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-138">a.</span></span> <span data-ttu-id="dcf0f-139">声明一个变量来存储 web 应用程序的名称。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="dcf0f-140">名称必须是唯一的以在默认 URL 中使用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="dcf0f-141">使用`$RANDOM`Bash 函数来构造名称可保证唯一性和结果的格式`webappname99999`。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="dcf0f-142">b.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-142">b.</span></span> <span data-ttu-id="dcf0f-143">创建资源组。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-143">Create a resource group.</span></span> <span data-ttu-id="dcf0f-144">资源组提供一种方法可聚合 Azure 资源作为一个组进行管理。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="dcf0f-145">`az`命令可调用[Azure CLI](/cli/azure/)。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="dcf0f-146">可以本地运行 CLI，但 Cloud Shell 中使用它可以节省时间和配置。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="dcf0f-147">c.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-147">c.</span></span> <span data-ttu-id="dcf0f-148">在 S1 层中创建应用服务计划。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="dcf0f-149">应用服务计划是共享相同的定价层的 web 应用的分组。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="dcf0f-150">S1 层不是免费的但它具有所需的过渡槽功能。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="dcf0f-151">d.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-151">d.</span></span> <span data-ttu-id="dcf0f-152">创建应用服务计划使用同一资源组中的 web 应用资源。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="dcf0f-153">e.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-153">e.</span></span> <span data-ttu-id="dcf0f-154">设置部署凭据。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-154">Set the deployment credentials.</span></span> <span data-ttu-id="dcf0f-155">这些部署凭据适用于你的订阅中的所有 web 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="dcf0f-156">不在用户名称中使用特殊字符。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="dcf0f-157">f.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-157">f.</span></span> <span data-ttu-id="dcf0f-158">将 web 应用配置为接受来自本地 Git 和显示部署*Git 部署 URL*。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="dcf0f-159">**记下此 URL 以供日后参考**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="dcf0f-160">g.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-160">g.</span></span> <span data-ttu-id="dcf0f-161">显示*web 应用 URL*。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-161">Display the *web app URL*.</span></span> <span data-ttu-id="dcf0f-162">浏览到此 URL 即可查看空白 web 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="dcf0f-163">**记下此 URL 以供日后参考**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="dcf0f-164">在本地计算机上使用命令行界面导航到 web 应用的项目文件夹 (例如， `.\simple-feed-reader\SimpleFeedReader`)。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="dcf0f-165">执行以下命令以设置 Git 推送到部署 URL:</span><span class="sxs-lookup"><span data-stu-id="dcf0f-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="dcf0f-166">a.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-166">a.</span></span> <span data-ttu-id="dcf0f-167">将远程 URL 添加到本地存储库。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="dcf0f-168">b.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-168">b.</span></span> <span data-ttu-id="dcf0f-169">推送本地*主*到分支*azure prod*远程数据库的*主*分支。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="dcf0f-170">系统会提示你之前创建的部署凭据。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="dcf0f-171">观察到命令行界面中的输出。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-171">Observe the output in the command shell.</span></span> <span data-ttu-id="dcf0f-172">Azure 远程生成 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="dcf0f-173">在浏览器中，导航到*Web 应用 URL*并记下已生成并部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="dcf0f-174">可以将其他的更改提交到本地 Git 存储库具有`git commit`。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="dcf0f-175">这些更改推送到 Azure 使用前面`git push`命令。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="dcf0f-176">使用 Visual Studio 部署</span><span class="sxs-lookup"><span data-stu-id="dcf0f-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="dcf0f-177">*注意：本部分仅适用于 Windows。Linux 和 macOS 用户应进行下面的步骤 2 中所述的更改。保存该文件，并将更改提交到本地存储库与`git commit`。最后，推送将更改与`git push`，如下所示的第一个部分。*</span><span class="sxs-lookup"><span data-stu-id="dcf0f-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="dcf0f-178">已从命令行界面部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="dcf0f-179">让我们使用 Visual Studio 的集成的工具将更新部署到应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="dcf0f-180">在后台，Visual Studio 的功能完全相同命令行工具，但在 Visual Studio 的熟悉用户界面内。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="dcf0f-181">打开*SimpleFeedReader.sln* Visual Studio 中。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="dcf0f-182">在解决方案资源管理器中打开*Pages\Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="dcf0f-183">更改`<h2>Simple Feed Reader</h2>`到`<h2>Simple Feed Reader - V2</h2>`。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="dcf0f-184">按**Ctrl**+**Shift**+**B**生成的应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="dcf0f-185">在解决方案资源管理器，右键单击项目并单击**发布**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![显示单击鼠标右键，发布的屏幕截图](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="dcf0f-187">Visual Studio 可以创建新的应用服务资源，但此更新将发布对现有部署。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="dcf0f-188">在**选取发布目标**对话框中，选择**应用服务**从列表中的左侧，然后选择**选择现有**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="dcf0f-189">单击“发布” 。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-189">Click **Publish**.</span></span>
6. <span data-ttu-id="dcf0f-190">在中**应用服务**对话框中，确认已显示在右上方的 Microsoft 或组织帐户用于创建你的 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="dcf0f-191">如果不是这样，单击下拉列表，并将其添加。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="dcf0f-192">确认正确的 Azure**订阅**处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="dcf0f-193">有关**视图**，选择**资源组**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="dcf0f-194">展开**AzureTutorial**资源组，然后选择现有的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="dcf0f-195">单击 **“确定”**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-195">Click **OK**.</span></span>

    ![屏幕截图显示发布的应用服务对话框](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="dcf0f-197">Visual Studio 生成，并将该应用部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="dcf0f-198">浏览到 web 应用 URL。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-198">Browse to the web app URL.</span></span> <span data-ttu-id="dcf0f-199">验证`<h2>`元素修改处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-199">Validate that the `<h2>` element modification is live.</span></span>

![应用程序与已更改标题](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="dcf0f-201">部署槽</span><span class="sxs-lookup"><span data-stu-id="dcf0f-201">Deployment slots</span></span>

<span data-ttu-id="dcf0f-202">部署槽而不会影响生产环境中运行的应用程序支持暂存的更改。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="dcf0f-203">通过质量保证团队验证应用的过渡的版本后, 可以交换生产和过渡槽。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="dcf0f-204">在过渡环境中的应用提升到生产环境中这种方式。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="dcf0f-205">以下步骤创建过渡槽，某些更改部署到它，并交换过渡槽与生产环境完成验证之后。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="dcf0f-206">登录到[Azure Cloud Shell](https://shell.azure.com/bash)，如果尚未登录。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="dcf0f-207">创建过渡槽。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-207">Create the staging slot.</span></span>

    <span data-ttu-id="dcf0f-208">a.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-208">a.</span></span> <span data-ttu-id="dcf0f-209">使用名称创建部署槽*暂存*。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="dcf0f-210">b.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-210">b.</span></span> <span data-ttu-id="dcf0f-211">配置要使用从本地 Git 和获取部署的过渡槽**暂存**部署 URL。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="dcf0f-212">**记下此 URL 以供日后参考**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="dcf0f-213">c.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-213">c.</span></span> <span data-ttu-id="dcf0f-214">显示过渡槽的 URL。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-214">Display the staging slot's URL.</span></span> <span data-ttu-id="dcf0f-215">浏览到 URL 即可查看空的过渡槽。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="dcf0f-216">**记下此 URL 以供日后参考**。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="dcf0f-217">在文本编辑器或 Visual Studio 中，修改*pages/Index.cshtml*再次以便`<h2>`元素中读取`<h2>Simple Feed Reader - V3</h2>`并保存该文件。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="dcf0f-218">将文件提交到本地 Git 存储库，使用任一**更改**Visual Studio 中的页*团队资源管理器*选项卡上，或通过输入以下使用本地计算机的命令行界面：</span><span class="sxs-lookup"><span data-stu-id="dcf0f-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="dcf0f-219">使用本地计算机的命令行界面，作为 Git 远程添加过渡的部署 URL，并推送已提交的更改：</span><span class="sxs-lookup"><span data-stu-id="dcf0f-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="dcf0f-220">a.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-220">a.</span></span> <span data-ttu-id="dcf0f-221">将过渡环境的远程 URL 添加到本地 Git 存储库。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="dcf0f-222">b.</span><span class="sxs-lookup"><span data-stu-id="dcf0f-222">b.</span></span> <span data-ttu-id="dcf0f-223">推送本地*主*到分支*azure 过渡环境*远程数据库的*主*分支。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="dcf0f-224">等待 Azure 生成并部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="dcf0f-225">若要验证 V3 已部署到过渡槽，请打开两个浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="dcf0f-226">在一个窗口中，导航到原始的 web 应用 URL。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="dcf0f-227">在其他窗口中，导航到过渡 web 应用 URL。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="dcf0f-228">生产 URL 提供服务的应用程序的 V2。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="dcf0f-229">过渡 URL 提供服务的应用程序的 V3。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-229">The staging URL serves V3 of the app.</span></span>

    ![比较浏览器窗口的屏幕截图](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="dcf0f-231">在 Cloud Shell 中，验证/上做好准备的增加将过渡槽交换到生产环境。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="dcf0f-232">验证交换发生通过刷新两个浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![交换后比较浏览器窗口](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="dcf0f-234">总结</span><span class="sxs-lookup"><span data-stu-id="dcf0f-234">Summary</span></span>

<span data-ttu-id="dcf0f-235">在本部分中，已完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="dcf0f-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="dcf0f-236">下载并构建示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="dcf0f-237">创建使用 Azure Cloud Shell 的 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="dcf0f-238">示例应用部署到 Azure 中使用 Git。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="dcf0f-239">部署到使用 Visual Studio 应用更改。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="dcf0f-240">添加到 web 应用的过渡槽。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="dcf0f-241">更新部署到过渡槽中。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="dcf0f-242">交换过渡和生产槽。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="dcf0f-243">在下一步部分中，将了解如何构建 DevOps 管道，其中包含 Azure 管道。</span><span class="sxs-lookup"><span data-stu-id="dcf0f-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="dcf0f-244">其他阅读材料</span><span class="sxs-lookup"><span data-stu-id="dcf0f-244">Additional reading</span></span>

* [<span data-ttu-id="dcf0f-245">Web 应用概述</span><span class="sxs-lookup"><span data-stu-id="dcf0f-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="dcf0f-246">生成 Azure 应用服务中的.NET Core 和 SQL 数据库 web 应用</span><span class="sxs-lookup"><span data-stu-id="dcf0f-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="dcf0f-247">为 Azure 应用服务配置部署凭据</span><span class="sxs-lookup"><span data-stu-id="dcf0f-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="dcf0f-248">设置过渡环境，在 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="dcf0f-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
