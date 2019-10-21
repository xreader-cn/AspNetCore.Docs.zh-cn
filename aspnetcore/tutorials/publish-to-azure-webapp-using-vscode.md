---
title: 使用 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure
author: ricardoserradas
description: 了解如何使用 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure 应用服务
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 90ba130f13903cd45eca062c0eca8945eff2e0fa
ms.sourcegitcommit: 7a2c820692f04bfba04398641b70f27fa15391f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290640"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="872bf-103">使用 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="872bf-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="872bf-104">作者：[Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="872bf-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="872bf-105">若要对应用服务部署问题进行故障排除，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="872bf-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="872bf-106">简介</span><span class="sxs-lookup"><span data-stu-id="872bf-106">Intro</span></span>

<span data-ttu-id="872bf-107">借助此教程，可了解如何创建 ASP.Net Core MVC 应用程序并在 Visual Studio Code 中部署。</span><span class="sxs-lookup"><span data-stu-id="872bf-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="872bf-108">设置</span><span class="sxs-lookup"><span data-stu-id="872bf-108">Set up</span></span>

- <span data-ttu-id="872bf-109">如果没有[免费 Azure 帐户](https://azure.microsoft.com/free/dotnet/)，请开设一个。</span><span class="sxs-lookup"><span data-stu-id="872bf-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="872bf-110">安装 [.NET Core SDK](https://dotnet.microsoft.com/download)</span><span class="sxs-lookup"><span data-stu-id="872bf-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="872bf-111">安装 [Visual Studio Code](https://code.visualstudio.com/Download)</span><span class="sxs-lookup"><span data-stu-id="872bf-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="872bf-112">将 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)安装到 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="872bf-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="872bf-113">将 [Azure 应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)安装到 Visual Studio Code 并在继续操作之前对其进行配置</span><span class="sxs-lookup"><span data-stu-id="872bf-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="872bf-114">创建 ASP.NET Core MVC 项目</span><span class="sxs-lookup"><span data-stu-id="872bf-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="872bf-115">使用终端导航到要在其中创建项目的文件夹，然后使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="872bf-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="872bf-116">将生成类似于以下的文件夹结构：</span><span class="sxs-lookup"><span data-stu-id="872bf-116">You'll have a folder structure similar to the following:</span></span>

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="872bf-117">使用 Visual Studio Code 打开</span><span class="sxs-lookup"><span data-stu-id="872bf-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="872bf-118">创建项目后，可使用以下选项之一通过 Visual Studio Code 将其打开：</span><span class="sxs-lookup"><span data-stu-id="872bf-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="872bf-119">通过命令行</span><span class="sxs-lookup"><span data-stu-id="872bf-119">Through the command line</span></span>

<span data-ttu-id="872bf-120">在创建项目的文件夹中使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="872bf-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="872bf-121">如果以下命令不起作用，请参考[此链接](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)，检查安装是否配置正确。</span><span class="sxs-lookup"><span data-stu-id="872bf-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="872bf-122">通过 Visual Studio Code 接口</span><span class="sxs-lookup"><span data-stu-id="872bf-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="872bf-123">打开 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="872bf-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="872bf-124">在菜单上，选择 `File > Open Folder`</span><span class="sxs-lookup"><span data-stu-id="872bf-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="872bf-125">选择创建 MVC 项目的文件夹的根目录</span><span class="sxs-lookup"><span data-stu-id="872bf-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="872bf-126">打开项目文件夹后，会收到消息，告知生成和调试所需的资产丢失。</span><span class="sxs-lookup"><span data-stu-id="872bf-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="872bf-127">接受帮助以添加资产。</span><span class="sxs-lookup"><span data-stu-id="872bf-127">Accept the help to add them.</span></span>

![加载了项目的 Visual Studio Code 接口](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="872bf-129">将在项目结构下创建 `.vscode` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="872bf-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="872bf-130">它将包含以下文件：</span><span class="sxs-lookup"><span data-stu-id="872bf-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="872bf-131">这些实用程序软件可帮助生成和调试 .NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="872bf-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="872bf-132">运行应用</span><span class="sxs-lookup"><span data-stu-id="872bf-132">Run the app</span></span>

<span data-ttu-id="872bf-133">将应用部署到 Azure 之前，请确保它在本地计算机上正常运行。</span><span class="sxs-lookup"><span data-stu-id="872bf-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="872bf-134">按 F5 运行项目</span><span class="sxs-lookup"><span data-stu-id="872bf-134">Press F5 to run the project</span></span>

<span data-ttu-id="872bf-135">Web 应用将在默认浏览器的新选项卡上开始运行。</span><span class="sxs-lookup"><span data-stu-id="872bf-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="872bf-136">它刚启动时可能会显示一个隐私问题警告。</span><span class="sxs-lookup"><span data-stu-id="872bf-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="872bf-137">这是因为将使用 HTTP 或 HTTPS 启动应用，会默认导航到 HTTPS 终结点。</span><span class="sxs-lookup"><span data-stu-id="872bf-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![本地调试应用时显示的隐私问题警告](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="872bf-139">若要保持调试会话，请单击 `Advanced` 然后单击 `Continue to localhost (unsafe)`。</span><span class="sxs-lookup"><span data-stu-id="872bf-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="872bf-140">本地生成部署包</span><span class="sxs-lookup"><span data-stu-id="872bf-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="872bf-141">打开 Visual Studio Code 终端</span><span class="sxs-lookup"><span data-stu-id="872bf-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="872bf-142">使用以下命令将 `Release` 包生成到名为 `publish` 的子文件夹中：</span><span class="sxs-lookup"><span data-stu-id="872bf-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="872bf-143">将在项目结构下创建新的 `publish` 文件夹</span><span class="sxs-lookup"><span data-stu-id="872bf-143">A new `publish` folder will be created under the project structure</span></span>

![发布文件夹结构](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="872bf-145">发布到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="872bf-145">Publish to Azure App Service</span></span>

<span data-ttu-id="872bf-146">利用适用于 Visual Studio Code 的 Azure 应用服务扩展，按照以下步骤直接将网站发布到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="872bf-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="872bf-147">如果要创建新的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="872bf-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="872bf-148">请右键单击 `publish` 文件夹，然后选择 `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="872bf-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="872bf-149">选择要创建 Web 应用的订阅</span><span class="sxs-lookup"><span data-stu-id="872bf-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="872bf-150">选择 `Create New Web App`</span><span class="sxs-lookup"><span data-stu-id="872bf-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="872bf-151">输入 Web 应用的名称</span><span class="sxs-lookup"><span data-stu-id="872bf-151">Enter a name for the Web App</span></span>

<span data-ttu-id="872bf-152">此扩展将创建新的 Web 应用，然后自动开始将包部署到该 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="872bf-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="872bf-153">完成部署后，请单击 `Browse Website` 以验证部署。</span><span class="sxs-lookup"><span data-stu-id="872bf-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![部署成功的消息](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="872bf-155">单击 `Browse Website` 后，将使用默认浏览器导航到此网站：</span><span class="sxs-lookup"><span data-stu-id="872bf-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![已成功部署新的 Web 应用](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="872bf-157">如果要部署到现有 Web 应用</span><span class="sxs-lookup"><span data-stu-id="872bf-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="872bf-158">请右键单击 `publish` 文件夹，然后选择 `Deploy to Web App...`</span><span class="sxs-lookup"><span data-stu-id="872bf-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="872bf-159">选择现有 Web 应用所在的订阅</span><span class="sxs-lookup"><span data-stu-id="872bf-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="872bf-160">从列表中选择 Web 应用</span><span class="sxs-lookup"><span data-stu-id="872bf-160">Select the Web App from the list</span></span>
- <span data-ttu-id="872bf-161">Visual Studio Code 将询问是否要覆盖现有内容。</span><span class="sxs-lookup"><span data-stu-id="872bf-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="872bf-162">单击 `Deploy` 以确认</span><span class="sxs-lookup"><span data-stu-id="872bf-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="872bf-163">该扩展将更新的内容部署到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="872bf-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="872bf-164">完成后，单击 `Browse Website` 以验证部署。</span><span class="sxs-lookup"><span data-stu-id="872bf-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![已成功部署现有 Web 应用](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="872bf-166">后续步骤</span><span class="sxs-lookup"><span data-stu-id="872bf-166">Next steps</span></span>

- [<span data-ttu-id="872bf-167">创建首个 Azure DevOps 管道</span><span class="sxs-lookup"><span data-stu-id="872bf-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="872bf-168">其他资源</span><span class="sxs-lookup"><span data-stu-id="872bf-168">Additional resources</span></span>

- [<span data-ttu-id="872bf-169">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="872bf-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="872bf-170">Azure 资源组</span><span class="sxs-lookup"><span data-stu-id="872bf-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)