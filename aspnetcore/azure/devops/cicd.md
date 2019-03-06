---
title: 持续集成和部署-使用 ASP.NET Core 和 Azure 进行开发运营
author: CamSoper
description: 持续集成和 DevOps 中使用 ASP.NET Core 和 Azure 的部署
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 906aae3fd4b4abd0becc8847b0f54c372bda300a
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346302"
---
# <a name="continuous-integration-and-deployment"></a>持续集成和部署

在上一章，您创建简单的源读取器应用的本地 Git 存储库。 在本章中，会将该代码发布到 GitHub 存储库并构建使用 Azure 管道的 Azure DevOps 服务管道。 管道可使持续生成和部署的应用程序。 GitHub 存储库的任何提交触发生成和部署到 Azure Web 应用的过渡槽。

在本部分中，将完成以下任务：

* 将应用程序的代码发布到 GitHub
* 断开连接本地 Git 部署
* 创建 Azure DevOps 组织
* 在 Azure DevOps 服务中创建团队项目
* 创建生成定义
* 创建发布管道
* 将更改提交到 GitHub 并自动部署到 Azure
* 检查 Azure 管道管道

## <a name="publish-the-apps-code-to-github"></a>将应用程序的代码发布到 GitHub

1. 打开浏览器窗口，并导航到`https://github.com`。
1. 单击 **+** 标头中的下拉列表中，选择**新的存储库**:

    ![新的 GitHub 存储库选项](media/cicd/github-new-repo.png)

1. 选择你的帐户**所有者**下拉列表中，并输入*简单源阅读器*中**存储库名称**文本框中。
1. 单击**创建存储库**按钮。
1. 打开本地计算机的命令行界面。 导航到在其中的目录*简单源阅读器*存储 Git 存储库。
1. 重命名现有*原点*到远程*上游*。 请执行以下命令：
    ```console
    git remote rename origin upstream
    ```
1. 添加一个新*原点*远程指向您的 GitHub 上的存储库副本。 请执行以下命令：
    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```
1. 将本地 Git 存储库发布到新创建的 GitHub 存储库。 请执行以下命令：
    ```console
    git push -u origin master
    ```
1. 打开浏览器窗口，并导航到`https://github.com/<GitHub_username>/simple-feed-reader/`。 验证你的代码会在 GitHub 存储库中。

## <a name="disconnect-local-git-deployment"></a>断开连接本地 Git 部署

删除本地 Git 部署通过执行以下步骤。 Azure 管道 （Azure DevOps 服务） 都将替换和增强该功能。

1. 打开[Azure 门户](https://portal.azure.com/)，并导航到*暂存 (mywebapp\<unique_number\>/暂存)* Web 应用。 可以通过输入快速位于 Web 应用*暂存*门户的搜索框中：

    ![过渡 Web 应用搜索词](media/cicd/portal-search-box.png)

1. 单击**部署中心**。 将显示新面板。 单击**断开连接**来删除已添加在上一章中的本地 Git 源控件配置。 通过单击确认删除操作**是**按钮。
1. 导航到*mywebapp < unique_number >* 应用服务。 请注意，可以使用门户的搜索框中快速找到应用服务。
1. 单击**部署中心**。 将显示新面板。 单击**断开连接**来删除已添加在上一章中的本地 Git 源控件配置。 通过单击确认删除操作**是**按钮。

## <a name="create-an-azure-devops-organization"></a>创建 Azure DevOps 组织

1. 打开浏览器并导航到[Azure DevOps 的组织创建页](https://go.microsoft.com/fwlink/?LinkId=307137)。
1. 键入唯一名称**选取一个易记名称**文本框中以形成用于访问你的 Azure DevOps 组织的 URL。
1. 选择**Git**单选按钮，因为代码托管在 GitHub 存储库。
1. 单击“继续”按钮。 稍等片刻、 帐户和团队项目之后, 名为*MyFirstProject*，创建。

    ![Azure DevOps 的组织创建页](media/cicd/vsts-account-creation.png)

1. 打开指示 Azure DevOps 的组织和项目可供使用的确认电子邮件。 单击**开始你的项目**按钮：

    ![启动项目按钮](media/cicd/vsts-start-project.png)

1. 浏览器将打开 *\<account_name\>。 visualstudio.com*。 单击*MyFirstProject*链接以开始配置项目的 DevOps 管道。

## <a name="configure-the-azure-pipelines-pipeline"></a>配置 Azure 管道管道

有三个不同的步骤才能完成。 完成操作的 DevOps 管道中的以下三个部分结果中的步骤。

### <a name="grant-azure-devops-access-to-the-github-repository"></a>授予 Azure DevOps 到 GitHub 存储库的访问权限

1. 展开**或从外部存储库的代码生成**accordion。 单击**安装程序生成**按钮：

    ![安装程序生成按钮](media/cicd/vsts-setup-build.png)

1. 选择**GitHub**选项从**选择一个源**部分：

    ![选择源-GitHub](media/cicd/vsts-select-source.png)

1. 授权是必需的 Azure DevOps 可以访问 GitHub 存储库之前。 输入 *< GitHub_username > GitHub 连接*中**连接名称**文本框中。 例如：

    ![GitHub 的连接名称](media/cicd/vsts-repo-authz.png)

1. 如果你的 GitHub 帐户启用双因素身份验证，则需要个人访问令牌。 在这种情况下，单击**使用 GitHub 个人访问令牌的授权**链接。 请参阅[正式 GitHub 个人访问令牌创建说明](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)有关的帮助。 仅*存储库*所需权限的作用域。 否则，请单击**使用 OAuth 授权**按钮。
1. 出现提示时，登录到你的 GitHub 帐户。 然后，选择授权授予对你的 Azure DevOps 组织访问权限。 如果成功，会创建新的服务终结点。
1. 单击省略号按钮旁边**存储库**按钮。 选择 *< GitHub_username > / 简单源阅读器*从列表中的存储库。 单击**选择**按钮。
1. 选择*主*从分支**手动和计划生成的默认分支**下拉列表。 单击“继续”按钮。 模板选择页会显示。

### <a name="create-the-build-definition"></a>创建生成定义

1. 从模板选择页中，输入*ASP.NET Core*在搜索框中：

    ![ASP.NET Core 模板页上的搜索](media/cicd/vsts-template-selection.png)

1. 显示模板搜索结果。 将鼠标悬停**ASP.NET Core**模板，然后单击**应用**按钮。
1. **任务**显示的生成定义选项卡。 单击“触发器”  选项卡。
1. 检查**启用持续集成**框。 下**的分支筛选器**部分中，确认**类型**下拉列表设置为*Include*。 设置**分支规范**下拉列表*master*。

    ![启用持续集成设置](media/cicd/vsts-enable-ci.png)

    这些设置会导致要进行任何更改推送到时触发的生成*主*GitHub 存储库分支。 在中测试持续集成[更改提交到 GitHub 并自动部署到 Azure](#commit-changes-to-github-and-automatically-deploy-to-azure)部分。

1. 单击**保存并排队**按钮，然后选择**保存**选项：

    ![保存按钮](media/cicd/vsts-save-build.png)

1. 下面的模式对话框会显示：

    ![保存生成定义的模式对话框](media/cicd/vsts-save-modal.png)

    使用的默认文件夹 *\\*，然后单击**保存**按钮。

### <a name="create-the-release-pipeline"></a>创建发布管道

1. 单击**版本**你的团队项目的选项卡。 单击**新的管道**按钮。

    ![版本选项卡的定义新建按钮](media/cicd/vsts-new-release-definition.png)

    模板选择窗格会显示。

1. 从模板选择页中，输入*应用服务*在搜索框中：

    ![发布管道模板搜索框](media/cicd/vsts-release-template-search.png)

1. 显示模板搜索结果。 将鼠标悬停**Azure 应用服务部署槽**模板，然后单击**应用**按钮。 **管道**发布管道的选项卡将出现。

    ![发布管道管道选项卡](media/cicd/vsts-release-definition-pipeline.png)

1. 单击**外**按钮**项目**框。 **添加项目**面板将显示：

    ![发布管道-添加项目面板](media/cicd/vsts-release-add-artifact.png)

1. 选择**构建**磁贴**源类型**部分。 此类型，用于对生成定义发布管道的链接。
1. 选择*MyFirstProject*从**项目**下拉列表。
1. 选择生成定义名称， *MyFirstProject ASP.NET Core CI*，从**源 （生成定义）** 下拉列表。
1. 选择*最新*从**默认版本**下拉列表。 此选项可用于构建生成的最后一次运行的生成定义的项目。
1. 替换中的文本**源别名**具有 textbox *Drop*。
1. 单击“添加”按钮。 **项目**部分更新以显示所做的更改。
1. 单击闪电形图标以启用持续部署：

    ![发布管道项目的闪电形图标](media/cicd/vsts-artifacts-lightning-bolt.png)

    启用此选项，每次新的生成时可执行部署。
1. 一个**持续部署触发器**面板右侧显示。 单击切换按钮以启用该功能。 但并不需要启用**拉取请求触发器**。
1. 单击**外**下拉列表中**版本分支筛选器**部分。 选择**生成定义的默认分支**选项。 此筛选器将导致仅对从 GitHub 存储库的生成触发发布*主*分支。
1. 单击“保存”按钮。 单击**确定**在随后出现的按钮**保存**模式对话框。
1. 单击**环境 1**框。 **环境**面板右侧显示。 更改*环境 1*中的文本**环境名称**的 textbox*生产*。

   ![发布管道的环境名称文本框](media/cicd/vsts-environment-name-textbox.png)

1. 单击**1 阶段，2 个任务**中的链接**生产**框：

    ![发布管道的生产环境 link.png](media/cicd/vsts-production-link.png)

    **任务**环境的选项卡将出现。
1. 单击**将 Azure 应用服务部署槽**任务。 在右侧面板中显示其设置。
1. 选择与中的应用服务相关联的 Azure 订阅**Azure 订阅**下拉列表。 选择后，单击**Authorize**按钮。
1. 选择*Web 应用*从**应用类型**下拉列表。
1. 选择*mywebapp / < unique_number / >* 从**应用服务名称**下拉列表。
1. 选择*AzureTutorial*从**资源组**下拉列表。
1. 选择*暂存*从**槽**下拉列表。
1. 单击“保存”按钮。
1. 将鼠标悬停默认发布管道名称。 单击铅笔图标以对其进行编辑。 使用*MyFirstProject ASP.NET Core CD*作为名称。

    ![发布管道名称](media/cicd/vsts-release-definition-name.png)

1. 单击“保存”按钮。

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>将更改提交到 GitHub 并自动部署到 Azure

1. 打开*SimpleFeedReader.sln* Visual Studio 中。
1. 在解决方案资源管理器中打开*Pages\Index.cshtml*。 更改`<h2>Simple Feed Reader - V3</h2>`到`<h2>Simple Feed Reader - V4</h2>`。
1. 按**Ctrl**+**Shift**+**B**生成的应用。
1. 将文件提交到 GitHub 存储库。 可以使用两种**更改**Visual Studio 中的页*团队资源管理器*选项卡上，或执行以下命令使用本地计算机的命令行界面：

    ```console
    git commit -a -m "upgraded to V4"
    ```
1. 将更改推入*主*到分支*原点*远程的 GitHub 存储库：

    ```console
    git push origin master
    ```

    GitHub 存储库中将显示提交*主*分支：

    ![主分支中的 GitHub 提交](media/cicd/github-commit.png)

    触发生成，因为在生成定义中启用持续集成**触发器**选项卡：

    ![启用持续集成](media/cicd/enable-ci.png)

1. 导航到**排队**选项卡**Azure 管道** > **生成**Azure DevOps 服务中的页。 已排队的生成显示分支和提交触发生成：

    ![排队的生成](media/cicd/build-queued.png)

1. 生成成功后，会发生到 Azure 的部署。 导航到在浏览器中的应用。 请注意，显示在标题中的"V4"文本：

    ![更新的应用程序](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>检查 Azure 管道管道

### <a name="build-definition"></a>生成定义

生成定义已创建了同名*MyFirstProject ASP.NET Core CI*。 完成后，生成过程将产生 *.zip*文件包括要发布的资产。 发布管道将这些资产部署到 Azure。

生成定义**任务**选项卡列出了正在使用的各个步骤。 有五个生成任务。

![生成定义任务](media/cicd/build-definition-tasks.png)

1. **还原**&mdash;执行`dotnet restore`命令还原应用程序的 NuGet 包。 默认包使用的源是 nuget.org。
1. **构建**&mdash;执行`dotnet build --configuration release`命令以编译应用程序的代码。 这`--configuration`选项用于生成代码，这是适用于部署到生产环境中的优化的版本。 修改*BuildConfiguration*在生成定义的变量**变量**选项卡上，如果需要调试配置，例如。
1. **测试**&mdash;执行`dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>`命令以运行应用的单元测试。 在 C# 项目匹配的任何执行单元测试的`**/*Tests/*.csproj`glob 模式。 在保存测试结果 *.trx*文件在指定的位置`--results-directory`选项。 如果任何测试失败，生成将失败，并且未部署。

    > [!NOTE]
    > 若要验证的单元测试工作，请修改*SimpleFeedReader.Tests\Services\NewsServiceTests.cs*特意中断的测试之一。 例如，更改`Assert.True(result.Count > 0);`到`Assert.False(result.Count > 0);`中`Returns_News_Stories_Given_Valid_Uri`方法。 提交并推送到 GitHub 的更改。 生成被触发，但失败。 生成管道状态将变为**失败**。 再次还原更改、 提交并推送。 生成成功。

1. **将发布**&mdash;执行`dotnet publish --configuration release --output <local_path_on_build_agent>`命令，生成 *.zip*要部署的项目文件。 `--output`选项指定的发布位置 *.zip*文件。 表示通过指定位置[预定义的变量](/azure/devops/pipelines/build/variables)名为`$(build.artifactstagingdirectory)`。 该变量将扩展到本地路径，如*c:\agent\_work\1\a*，生成代理上。
1. **发布项目** &mdash; Publishes *.zip*生成文件**发布**任务。 该任务接受 *.zip*文件作为参数，这是预定义的变量的位置`$(build.artifactstagingdirectory)`。 *.Zip*文件发布为一个名为文件夹*drop*。

单击生成定义**摘要**链接以查看与定义的生成历史记录：

![屏幕截图显示生成定义历史记录](media/cicd/build-definition-summary.png)

在结果页上，单击与唯一的内部版本号对应的链接：

![屏幕截图显示生成定义摘要页](media/cicd/build-definition-completed.png)

显示此特定生成的摘要。 单击**项目**选项卡，并请注意*drop*列出由生成内容生成的文件夹：

![显示生成定义项目的放置文件夹的屏幕截图](media/cicd/build-definition-artifacts.png)

使用**下载**并**浏览**检查已发布的项目的链接。

### <a name="release-pipeline"></a>发布管道

发布管道已创建了同名*MyFirstProject ASP.NET Core CD*:

![屏幕截图显示发行管道概述](media/cicd/release-definition-overview.png)

发布管道的两个主要组件是**项目**并**环境**。 单击框中的**项目**部分会显示以下窗格：

![屏幕截图显示发布管道项目](media/cicd/release-definition-artifacts.png)

**源 （生成定义）** 值表示此发布管道链接到生成定义。 *.Zip*成功运行的生成定义所生成文件提供给*生产*环境以部署到 Azure。 单击*1 阶段，2 个任务*中的链接*生产*环境，若要查看发布管道任务：

![屏幕截图显示发布管道任务](media/cicd/release-definition-tasks.png)

发布管道包括两个任务：*将 Azure 应用服务部署到槽*并*管理 Azure 应用服务-槽交换*。 单击第一个任务将显示下面的任务配置：

![屏幕截图显示发布管道部署任务](media/cicd/release-definition-task1.png)

Azure 订阅、 服务类型、 web 应用名称、 资源组和部署槽部署任务中定义。 **包或文件夹**文本框中保留 *.zip*要提取并部署到的文件路径*暂存*槽*mywebapp\<唯一数目 （_n)\>*  web 应用。

单击该槽交换任务将显示下面的任务配置：

![屏幕截图显示发布管道槽交换任务](media/cicd/release-definition-task2.png)

提供订阅、 资源组、 服务类型、 web 应用名称和部署槽详细信息。 **与生成交换**选中复选框。 因此，将位部署到*暂存*槽交换到生产环境。

## <a name="additional-reading"></a>其他阅读材料

* [使用 Azure Pipelines 创建你的第一个管道](/azure/devops/pipelines/get-started-yaml)
* [生成和.NET Core 项目](/azure/devops/pipelines/languages/dotnet-core)
* [部署 web 应用与 Azure 管道](/azure/devops/pipelines/targets/webapp)
