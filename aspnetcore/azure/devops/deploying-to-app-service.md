---
title: 将应用部署到应用服务 - 使用 ASP.NET Core 和 Azure 实施 DevOps
author: CamSoper
description: 要使用 ASP.NET Core 和 Azure 实施 DevOps，首先是将 ASP.NET Core 应用部署到 Azure 应用服务。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646554"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="b7e8c-103">将应用部署到应用服务</span><span class="sxs-lookup"><span data-stu-id="b7e8c-103">Deploy an app to App Service</span></span>

<span data-ttu-id="b7e8c-104">[Azure 应用服务](/azure/app-service/)是 Azure 的 Web 托管平台。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="b7e8c-105">将 Web 应用部署到 Azure 应用服务的操作可手动完成，也可自动处理。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="b7e8c-106">本指南的该部分介绍了一些部署方法，它们可使用命令行手动触发或按脚本触发，也可使用 Visual Studio 手动触发。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="b7e8c-107">在本部分中，你将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="b7e8c-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="b7e8c-108">下载并构建示例应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-108">Download and build the sample app.</span></span>
* <span data-ttu-id="b7e8c-109">使用 Azure Cloud Shell 创建 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="b7e8c-110">使用 Git 将示例应用部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="b7e8c-111">使用 Visual Studio 将更改部署到应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="b7e8c-112">将过渡槽添加到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="b7e8c-113">将更新部署到过渡槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="b7e8c-114">交换过渡槽和生产槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="b7e8c-115">下载和测试应用</span><span class="sxs-lookup"><span data-stu-id="b7e8c-115">Download and test the app</span></span>

<span data-ttu-id="b7e8c-116">本指南使用的是预生成的 ASP.NET Core 应用，即[简单源读取器](https://github.com/Azure-Samples/simple-feed-reader/)。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="b7e8c-117">它是一种 Razor Pages 应用，使用 `Microsoft.SyndicationFeed.ReaderWriter` API 来检索 RSS/Atom 源并在列表中显示消息项。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="b7e8c-118">可随意查看代码，但请务必了解此应用并无特别之处。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="b7e8c-119">它只是一个简单的、用于说明的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="b7e8c-120">在命令行界面中，按如下方式下载代码、生成项目并运行该项目。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="b7e8c-121">*注意：* Linux/macOS 用户应对路径进行适当的更改，例如使用正斜杠 (`/`) 而不是反斜杠 (`\`)。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="b7e8c-122">将代码复制到本地计算机上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="b7e8c-123">将工作文件夹更改为刚创建的“简单源读取器”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="b7e8c-124">还原包并生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="b7e8c-125">运行应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![dotnet run 命令运行成功](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="b7e8c-127">打开浏览器并导航到 `http://localhost:5000` 。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="b7e8c-128">通过应用，可键入或粘贴联合源 URL，还可查看消息项列表。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![显示 RSS 源内容的应用](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="b7e8c-130">如果确信应用运行正常，请在命令行界面中按 Ctrl+C 将其关闭   。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="b7e8c-131">创建 Azure 应用服务 Web 应用</span><span class="sxs-lookup"><span data-stu-id="b7e8c-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="b7e8c-132">要部署应用，需要创建一个应用服务 [Web 应用](/azure/app-service/app-service-web-overview)。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="b7e8c-133">创建 Web 应用后，你将使用 Git 从本地计算机部署到该应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="b7e8c-134">登录到 [Azure Cloud Shell](https://shell.azure.com/bash)。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="b7e8c-135">注意：首次登录时，Cloud Shell 会提示为配置文件创建存储帐户。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="b7e8c-136">请接受默认名称或提供唯一名称。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="b7e8c-137">在 Cloud Shell 中执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="b7e8c-138">a.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-138">a.</span></span> <span data-ttu-id="b7e8c-139">声明一个变量，用于存储 Web 应用的名称。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="b7e8c-140">该名称必须是唯一的，才能在默认 URL 中使用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="b7e8c-141">使用 `$RANDOM` Bash 函数构造名称可确保唯一性且生成 `webappname99999` 格式的结果。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="b7e8c-142">b.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-142">b.</span></span> <span data-ttu-id="b7e8c-143">创建资源组。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-143">Create a resource group.</span></span> <span data-ttu-id="b7e8c-144">通过资源组可将 Azure 资源聚合起来，以组的形式进行管理。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="b7e8c-145">`az` 命令会调用 [Azure CLI](/cli/azure/)。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="b7e8c-146">尽管 CLI 可本地运行，但在 Cloud Shell 中使用可省时省配置。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="b7e8c-147">c.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-147">c.</span></span> <span data-ttu-id="b7e8c-148">在 S1 层级创建应用服务计划。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="b7e8c-149">应用服务计划是一组共享同一定价层的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="b7e8c-150">S1 层级并不免费，但却是过渡槽功能所必需的。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="b7e8c-151">d.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-151">d.</span></span> <span data-ttu-id="b7e8c-152">使用同一资源组中的应用服务计划创建 Web 应用资源。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="b7e8c-153">e.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-153">e.</span></span> <span data-ttu-id="b7e8c-154">设置部署凭据。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-154">Set the deployment credentials.</span></span> <span data-ttu-id="b7e8c-155">这些部署凭据适用于订阅中的所有 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="b7e8c-156">用户名中不要使用特殊字符。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="b7e8c-157">f.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-157">f.</span></span> <span data-ttu-id="b7e8c-158">将 Web 应用配置为接受本地 Git 部署并显示 Git 部署 URL  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="b7e8c-159">请记下此 URL 供稍后参考  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="b7e8c-160">g.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-160">g.</span></span> <span data-ttu-id="b7e8c-161">显示 Web 应用 URL  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-161">Display the *web app URL*.</span></span> <span data-ttu-id="b7e8c-162">访问此 URL，查看空白 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="b7e8c-163">请记下此 URL 供稍后参考  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="b7e8c-164">使用本地计算机上的命令行界面导航到 Web 应用的项目文件夹（例如 `.\simple-feed-reader\SimpleFeedReader`）。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="b7e8c-165">执行以下命令，将 Git 设置为推送到部署 URL：</span><span class="sxs-lookup"><span data-stu-id="b7e8c-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="b7e8c-166">a.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-166">a.</span></span> <span data-ttu-id="b7e8c-167">将远程 URL 添加到本地存储库。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="b7e8c-168">b.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-168">b.</span></span> <span data-ttu-id="b7e8c-169">将本地 master 分支推送到 azure-prod 远程的 master 分支    。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="b7e8c-170">系统将提示使用前面创建的部署凭据。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="b7e8c-171">查看命令行界面中的输出。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-171">Observe the output in the command shell.</span></span> <span data-ttu-id="b7e8c-172">Azure 会远程构建 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="b7e8c-173">在浏览器中，导航到 Web 应用 URL 并注意应用是否已构建且已经过部署  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="b7e8c-174">使用 `git commit` 可将其他更改提交到本地 Git 存储库。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="b7e8c-175">使用前面的 `git push` 命令可将这些更改推送到 Azure。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="b7e8c-176">使用 Visual Studio 进行部署</span><span class="sxs-lookup"><span data-stu-id="b7e8c-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="b7e8c-177">*注意：本部分仅适用于 Windows。Linux 和 macOS 用户应进行下面步骤 2 中所述的更改。保存文件，然后使用 `git commit` 将更改提交到本地存储库。最后如第一部分所述，使用 `git push` 推送更改*。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="b7e8c-178">已从命令行界面部署了应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="b7e8c-179">接下来使用 Visual Studio 的集成工具将更新部署到应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="b7e8c-180">在后台，Visual Studio 会实现与命令行工具相同的操作，但是在 Visual Studio 的常见 UI 中完成的。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="b7e8c-181">在 Visual Studio 中打开 SimpleFeedReader.sln  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="b7e8c-182">在解决方案资源管理器中，打开 Pages\Index.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="b7e8c-183">将 `<h2>Simple Feed Reader</h2>` 更改为 `<h2>Simple Feed Reader - V2</h2>`。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="b7e8c-184">按 Ctrl+Shift+B 构建应用    。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="b7e8c-185">在解决方案资源管理器中，右键单击该项目并单击“发布”  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![显示右键单击和“发布”的屏幕截图](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="b7e8c-187">Visual Studio 可创建新的应用服务资源，但此更新将通过现有部署发布。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="b7e8c-188">在“选取发布目标”对话框中，选择左侧列表中的“应用服务”，再选择“选择现有项”    。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="b7e8c-189">单击“发布”  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-189">Click **Publish**.</span></span>
6. <span data-ttu-id="b7e8c-190">在“应用服务”对话框中，确认右上角显示了用于创建 Azure 订阅的 Microsoft 帐户或组织帐户  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="b7e8c-191">如果未显示，请单击下拉箭头并进行添加。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="b7e8c-192">确认已选择正确的 Azure 订阅  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="b7e8c-193">在“视图”部分，选择“资源组”   。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="b7e8c-194">展开 AzureTutorial 资源组，然后选择现有的 Web 应用  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="b7e8c-195">单击 **“确定”** 。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-195">Click **OK**.</span></span>

    ![显示“发布应用服务”对话框的屏幕截图](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="b7e8c-197">Visual Studio 会构建应用并将其部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="b7e8c-198">访问 Web 应用 URL。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-198">Browse to the web app URL.</span></span> <span data-ttu-id="b7e8c-199">验证 `<h2>` 元素修改是否有效。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-199">Validate that the `<h2>` element modification is live.</span></span>

![标题已更改的应用](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="b7e8c-201">部署槽位</span><span class="sxs-lookup"><span data-stu-id="b7e8c-201">Deployment slots</span></span>

<span data-ttu-id="b7e8c-202">部署槽位支持更改过渡，且不会影响生产环境中运行的应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="b7e8c-203">在质量保证团队验证过渡版应用后，就可交换生产槽和过渡槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="b7e8c-204">过渡期应用可以这种方式推广到生产槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="b7e8c-205">以下步骤将创建一个过渡槽，对过渡槽部署一些更改，然后在验证后交换过渡槽和生产槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="b7e8c-206">如果尚未登录 [Azure Cloud Shell](https://shell.azure.com/bash)，请先登录。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="b7e8c-207">创建过渡槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-207">Create the staging slot.</span></span>

    <span data-ttu-id="b7e8c-208">a.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-208">a.</span></span> <span data-ttu-id="b7e8c-209">创建名为“过渡”的部署槽位  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="b7e8c-210">b.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-210">b.</span></span> <span data-ttu-id="b7e8c-211">将过渡槽配置为使用本地 Git 部署并获取过渡部署 URL  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="b7e8c-212">请记下此 URL 供稍后参考  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="b7e8c-213">c.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-213">c.</span></span> <span data-ttu-id="b7e8c-214">显示过渡槽的 URL。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-214">Display the staging slot's URL.</span></span> <span data-ttu-id="b7e8c-215">访问 URL，查看空过渡槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="b7e8c-216">请记下此 URL 供稍后参考  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="b7e8c-217">在文本编辑器或 Visual Studio 中，再次修改 Pages/Index.cshtml，以便 `<h2>` 元素读取 `<h2>Simple Feed Reader - V3</h2>` 并保存文件  。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="b7e8c-218">将文件提交到本地 Git 存储库，方式是使用 Visual Studio“团队资源管理器”选项卡中的“更改”页面，或者使用本地计算机的命令行界面输入以下内容   ：</span><span class="sxs-lookup"><span data-stu-id="b7e8c-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="b7e8c-219">使用本地计算机的命令行界面将过渡部署 URL 添加为 Git 远程，并推送已提交的更改：</span><span class="sxs-lookup"><span data-stu-id="b7e8c-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="b7e8c-220">a.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-220">a.</span></span> <span data-ttu-id="b7e8c-221">将过渡的远程 URL 添加到本地 Git 存储库。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="b7e8c-222">b.</span><span class="sxs-lookup"><span data-stu-id="b7e8c-222">b.</span></span> <span data-ttu-id="b7e8c-223">将本地 master 分支推送到 azure-staging 远程的 master 分支    。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="b7e8c-224">请在 Azure 构建和部署应用时稍事等待。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="b7e8c-225">若要验证 V3 是否已部署到过渡槽，请打开两个浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="b7e8c-226">在其中一个窗口中，访问初始 Web 应用 URL。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="b7e8c-227">在另一个窗口中，访问过渡 Web 应用 URL。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="b7e8c-228">生产 URL 适用于应用的 V2。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="b7e8c-229">过渡 URL 适用于应用的 V3。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-229">The staging URL serves V3 of the app.</span></span>

    ![对比浏览器窗口的屏幕截图](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="b7e8c-231">在 Cloud Shell 中，将已验证/已准备好的过渡槽交换到生产环境。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="b7e8c-232">刷新两个浏览器窗口，验证是否进行了交换。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![对比交换后的浏览器窗口](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="b7e8c-234">总结</span><span class="sxs-lookup"><span data-stu-id="b7e8c-234">Summary</span></span>

<span data-ttu-id="b7e8c-235">在本部分中完成了以下任务：</span><span class="sxs-lookup"><span data-stu-id="b7e8c-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="b7e8c-236">下载并构建了示例应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="b7e8c-237">使用 Azure Cloud Shell 创建了 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="b7e8c-238">使用 Git 将示例应用部署到了 Azure。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="b7e8c-239">使用 Visual Studio 将更改部署到了应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="b7e8c-240">将过渡槽添加到了 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="b7e8c-241">将更新部署到了过渡槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="b7e8c-242">交换了过渡槽和生产槽。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="b7e8c-243">在下一部分，你将了解如何使用 Azure Pipelines 构建 DevOps 管道。</span><span class="sxs-lookup"><span data-stu-id="b7e8c-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="b7e8c-244">其他阅读材料</span><span class="sxs-lookup"><span data-stu-id="b7e8c-244">Additional reading</span></span>

* [<span data-ttu-id="b7e8c-245">Web 应用概述</span><span class="sxs-lookup"><span data-stu-id="b7e8c-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="b7e8c-246">在 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用</span><span class="sxs-lookup"><span data-stu-id="b7e8c-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="b7e8c-247">为 Azure 应用服务配置部署凭据</span><span class="sxs-lookup"><span data-stu-id="b7e8c-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="b7e8c-248">设置 Azure 应用服务中的过渡环境</span><span class="sxs-lookup"><span data-stu-id="b7e8c-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
