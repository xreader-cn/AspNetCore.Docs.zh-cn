---
title: 将应用部署到应用服务-使用 ASP.NET Core 和 Azure 进行开发运营
author: CamSoper
description: 将 ASP.NET Core 应用部署到 Azure 应用服务中，使用 ASP.NET Core 和 Azure 进行开发运营的第一步。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: e09d03f1d30f128b1db1588aa92b28ec3e4ae626
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892634"
---
# <a name="deploy-an-app-to-app-service"></a>将应用部署到应用服务

[Azure 应用服务](/azure/app-service/)是 Azure 的 web 托管平台。 手动或通过自动化过程，可以完成 web 应用部署到 Azure 应用服务。 指南的本部分讨论可以手动或使用命令行中，脚本触发或触发手动使用 Visual Studio 的部署方法。

在本部分中，将完成以下任务：

* 下载并生成示例应用程序。
* 创建使用 Azure Cloud Shell 的 Azure 应用服务 Web 应用。
* 将示例应用程序部署到 Azure 中使用 Git。
* 将更改部署到使用 Visual Studio 的应用。
* 将过渡槽添加到 web 应用。
* 将更新部署到过渡槽。
* 交换过渡和生产槽。

## <a name="download-and-test-the-app"></a>下载并测试应用程序

本指南中使用的应用是一个预构建的 ASP.NET Core 应用，[简单馈送读取器](https://github.com/Azure-Samples/simple-feed-reader/)。 它是使用 Razor 页面应用`Microsoft.SyndicationFeed.ReaderWriter`API 以检索 RSS/Atom 馈送，并在列表中显示的新闻项。

随意查看代码，但请务必了解，没有什么特别此应用之处。 正是出于演示目的只是简单的 ASP.NET Core 应用。

从命令行界面，下载的代码、 生成项目时，和运行它，如下所示。

> *注意：Linux/macOS 用户应更改相应的路径，例如，使用正斜杠 (`/`) 而不是反斜杠 (`\`)。*

1. 代码克隆到本地计算机上的文件夹。

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. 更改到您的工作文件夹*简单源阅读器*已创建的文件夹。

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. 还原包，并生成解决方案。

    ```console
    dotnet build
    ```

4. 运行应用。

    ```console
    dotnet run
    ```

    ![Dotnet run 命令成功](./media/deploying-to-app-service/dotnet-run.png)

5. 打开浏览器并导航到`http://localhost:5000`。 应用程序，可键入或粘贴联合源 URL，并查看新闻项的列表。

     ![应用程序显示 RSS 源的内容](./media/deploying-to-app-service/app-in-browser.png)

6. 感到满意后应用程序是否正常工作，通过按关闭它**Ctrl**+**C**命令行界面中。

## <a name="create-the-azure-app-service-web-app"></a>创建 Azure 应用服务 Web 应用

若要部署应用程序，你将需要创建应用服务[Web 应用](/azure/app-service/app-service-web-overview)。 创建后的 Web 应用，你将部署到它从使用 Git 在本地计算机。

1. 登录到[Azure Cloud Shell](https://shell.azure.com/bash)。 注意:在登录时第一次，Cloud Shell 会提示创建配置文件的存储帐户。 接受默认值或提供唯一的名称。

2. 对于以下步骤使用 Cloud Shell。

    a. 声明一个变量来存储 web 应用程序的名称。 名称必须是唯一的以在默认 URL 中使用。 使用`$RANDOM`Bash 函数来构造名称可保证唯一性和结果的格式`webappname99999`。

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. 创建资源组。 资源组提供一种方法可聚合 Azure 资源作为一个组进行管理。

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    `az`命令可调用[Azure CLI](/cli/azure/)。 可以本地运行 CLI，但 Cloud Shell 中使用它可以节省时间和配置。

    c. 在 S1 层中创建应用服务计划。 应用服务计划是共享相同的定价层的 web 应用的分组。 S1 层不是免费的但它具有所需的过渡槽功能。

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. 创建应用服务计划使用同一资源组中的 web 应用资源。

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. 设置部署凭据。 这些部署凭据适用于你的订阅中的所有 web 应用。 不在用户名称中使用特殊字符。

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. 将 web 应用配置为接受来自本地 Git 和显示部署*Git 部署 URL*。 **记下此 URL 以供日后参考**。

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. 显示*web 应用 URL*。 浏览到此 URL 即可查看空白 web 应用。 **记下此 URL 以供日后参考**。

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. 在本地计算机上使用命令行界面导航到 web 应用的项目文件夹 (例如， `.\simple-feed-reader\SimpleFeedReader`)。 执行以下命令以设置 Git 推送到部署 URL:

    a. 将远程 URL 添加到本地存储库。

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. 推送本地*主*到分支*azure prod*远程数据库的*主*分支。

    ```console
    git push azure-prod master
    ```

    系统会提示你之前创建的部署凭据。 观察到命令行界面中的输出。 Azure 远程生成 ASP.NET Core 应用。

4. 在浏览器中，导航到*Web 应用 URL*并记下已生成并部署应用程序。 可以将其他的更改提交到本地 Git 存储库具有`git commit`。 这些更改推送到 Azure 使用前面`git push`命令。

## <a name="deployment-with-visual-studio"></a>使用 Visual Studio 部署

> *注意：本部分仅适用于 Windows。Linux 和 macOS 用户应进行下面的步骤 2 中所述的更改。保存该文件，并将更改提交到本地存储库与`git commit`。最后，推送将更改与`git push`，如下所示的第一个部分。*

已从命令行界面部署应用程序。 让我们使用 Visual Studio 的集成的工具将更新部署到应用。 在后台，Visual Studio 的功能完全相同命令行工具，但在 Visual Studio 的熟悉用户界面内。

1. 打开*SimpleFeedReader.sln* Visual Studio 中。
2. 在解决方案资源管理器中打开*Pages\Index.cshtml*。 更改`<h2>Simple Feed Reader</h2>`到`<h2>Simple Feed Reader - V2</h2>`。
3. 按**Ctrl**+**Shift**+**B**生成的应用。
4. 在解决方案资源管理器，右键单击项目并单击**发布**。

    ![显示单击鼠标右键，发布的屏幕截图](./media/deploying-to-app-service/publish.png)
5. Visual Studio 可以创建新的应用服务资源，但此更新将发布对现有部署。 在**选取发布目标**对话框中，选择**应用服务**从列表中的左侧，然后选择**选择现有**。 单击“发布” 。
6. 在中**应用服务**对话框中，确认已显示在右上方的 Microsoft 或组织帐户用于创建你的 Azure 订阅。 如果不是这样，单击下拉列表，并将其添加。
7. 确认正确的 Azure**订阅**处于选中状态。 有关**视图**，选择**资源组**。 展开**AzureTutorial**资源组，然后选择现有的 web 应用。 单击 **“确定”**。

    ![屏幕截图显示发布的应用服务对话框](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio 生成，并将该应用部署到 Azure。 浏览到 web 应用 URL。 验证`<h2>`元素修改处于活动状态。

![应用程序与已更改标题](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>部署槽

部署槽而不会影响生产环境中运行的应用程序支持暂存的更改。 通过质量保证团队验证应用的过渡的版本后, 可以交换生产和过渡槽。 在过渡环境中的应用提升到生产环境中这种方式。 以下步骤创建过渡槽，某些更改部署到它，并交换过渡槽与生产环境完成验证之后。

1. 登录到[Azure Cloud Shell](https://shell.azure.com/bash)，如果尚未登录。
2. 创建过渡槽。

    a. 使用名称创建部署槽*暂存*。

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. 配置要使用从本地 Git 和获取部署的过渡槽**暂存**部署 URL。 **记下此 URL 以供日后参考**。

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. 显示过渡槽的 URL。 浏览到 URL 即可查看空的过渡槽。 **记下此 URL 以供日后参考**。

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. 在文本编辑器或 Visual Studio 中，修改*pages/Index.cshtml*再次以便`<h2>`元素中读取`<h2>Simple Feed Reader - V3</h2>`并保存该文件。

4. 将文件提交到本地 Git 存储库，使用任一**更改**Visual Studio 中的页*团队资源管理器*选项卡上，或通过输入以下使用本地计算机的命令行界面：

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. 使用本地计算机的命令行界面，作为 Git 远程添加过渡的部署 URL，并推送已提交的更改：

    a. 将过渡环境的远程 URL 添加到本地 Git 存储库。

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. 推送本地*主*到分支*azure 过渡环境*远程数据库的*主*分支。

    ```console
    git push azure-staging master
    ```

    等待 Azure 生成并部署应用程序。

6. 若要验证 V3 已部署到过渡槽，请打开两个浏览器窗口。 在一个窗口中，导航到原始的 web 应用 URL。 在其他窗口中，导航到过渡 web 应用 URL。 生产 URL 提供服务的应用程序的 V2。 过渡 URL 提供服务的应用程序的 V3。

    ![比较浏览器窗口的屏幕截图](./media/deploying-to-app-service/ready-to-swap.png)

7. 在 Cloud Shell 中，验证/上做好准备的增加将过渡槽交换到生产环境。

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. 验证交换发生通过刷新两个浏览器窗口。

    ![交换后比较浏览器窗口](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>总结

在本部分中，已完成以下任务：

* 下载并构建示例应用程序。
* 创建使用 Azure Cloud Shell 的 Azure 应用服务 Web 应用。
* 示例应用部署到 Azure 中使用 Git。
* 部署到使用 Visual Studio 应用更改。
* 添加到 web 应用的过渡槽。
* 更新部署到过渡槽中。
* 交换过渡和生产槽。

在下一步部分中，将了解如何构建 DevOps 管道，其中包含 Azure 管道。

## <a name="additional-reading"></a>其他阅读材料

* [Web 应用概述](/azure/app-service/app-service-web-overview)
* [生成 Azure 应用服务中的.NET Core 和 SQL 数据库 web 应用](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [为 Azure 应用服务配置部署凭据](/azure/app-service/app-service-deployment-credentials)
* [设置过渡环境，在 Azure 应用服务](/azure/app-service/web-sites-staged-publishing)
