---
title: 持续集成和持续部署 - 通过 ASP.NET Core 和 Azure 实现 DevOps
author: CamSoper
description: 在 DevOps 中使用 ASP.NET Core 和 Azure 进行持续集成和持续部署
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
no-loc:
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
uid: azure/devops/cicd
ms.openlocfilehash: 3632f1c4bd419aae08105005de3d81fc2cb9e410
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625877"
---
# <a name="continuous-integration-and-deployment"></a>持续集成和持续部署

在上一章中，你为简单源读取器应用创建了一个本地 Git 存储库。 在本章中，你将把代码发布到 GitHub 存储库，并使用 Azure Pipelines 构建 Azure DevOps Services 管道。 管道支持应用的持续生成和持续部署。 对 GitHub 存储库的任何提交都会触发对 Azure Web 应用过渡槽的生成和部署。

在本节中，你将完成以下任务：

* 将应用的代码发布到 GitHub
* 断开本地 Git 部署连接
* 创建一个 Azure DevOps 组织
* 在 Azure DevOps Services 中创建团队项目
* 创建生成定义
* 创建发布管道
* 提交对 GitHub 所做的更改并将其自动部署到 Azure
* 检查 Azure Pipelines 管道

## <a name="publish-the-apps-code-to-github"></a>将应用的代码发布到 GitHub

1. 打开浏览器窗口并导航到 `https://github.com`。
1. 单击标头中的 + 下拉列表，然后选择“新建存储库” ：

    ![GitHub“新建存储库”选项](media/cicd/github-new-repo.png)

1. 在“所有者”下拉列表中选择你的帐户，然后在“存储库名称”文本框中输入“简单源读取器” 。
1. 单击“创建存储库”按钮。
1. 打开本地计算机的命令行界面。 导航到存储简单源读取器 Git 存储库的目录。
1. 将现有的源远程库重命名为“上游” 。 请执行以下命令：

    ```console
    git remote rename origin upstream
    ```

1. 添加指向一个 GitHub 上存储库的副本的新源远程库。 请执行以下命令：

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. 将本地 Git 存储库发布到新创建的 GitHub 存储库。 请执行以下命令：

    ```console
    git push -u origin master
    ```

1. 打开浏览器窗口并导航到 `https://github.com/<GitHub_username>/simple-feed-reader/`。 验证你的代码是否显示在 GitHub 存储库中。

## <a name="disconnect-local-git-deployment"></a>断开本地 Git 部署连接

通过以下步骤删除本地 Git 部署。 Azure Pipelines（一种 Azure DevOps 服务）既替代又增强了该功能。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到暂存 (mywebapp\<unique_number\>/staging) Web 应用。 通过在门户的搜索框中输入“暂存”，可以快速找到该 Web 应用：

    ![暂存 Web 应用搜索术语](media/cicd/portal-search-box.png)

1. 单击“部署中心”。 此时将显示一个新面板。 单击“断开连接”以删除上一章中添加的本地 Git 源代码管理配置。 单击“是”按钮，确认删除操作。
1. 导航到“mywebapp<unique_number>”应用服务。 在此提醒，可使用门户的搜索框快速找到该应用服务。
1. 单击“部署中心”。 此时将显示一个新面板。 单击“断开连接”以删除上一章中添加的本地 Git 源代码管理配置。 单击“是”按钮，确认删除操作。

## <a name="create-an-azure-devops-organization"></a>创建一个 Azure DevOps 组织

1. 打开浏览器，并导航到 [Azure DevOps 组织创建页](https://go.microsoft.com/fwlink/?LinkId=307137)。
1. 在“选取一个便于记忆的名称”文本框中键入一个唯一名称，形成用于访问 Azure DevOps 组织的 URL。
1. 选择“Git”单选按钮，因为代码托管在 GitHub 存储库中。
1. 单击“继续”按钮。 片刻后，一个帐户以及一个名为 MyFirstProject 的团队项目随之创建。

    ![Azure DevOps 组织创建页](media/cicd/vsts-account-creation.png)

1. 打开确认电子邮件，该邮件指示 Azure DevOps 组织和项目已准备就绪，可供使用。 单击“启动项目”按钮：

    ![“启动项目”按钮](media/cicd/vsts-start-project.png)

1. 浏览器将打开 \<account_name\>.visualstudio.com。 单击“MyFirstProject”链接，开始配置项目的 DevOps 管道。

## <a name="configure-the-azure-pipelines-pipeline"></a>配置 Azure Pipelines 管道

需要完成三个不同的步骤。 完成以下三节中的步骤将生成一个可操作的 DevOps 管道。

### <a name="grant-azure-devops-access-to-the-github-repository"></a>授予 Azure DevOps 对 GitHub 存储库的访问权限

1. 展开“或从外部存储库生成代码”可折叠项。 单击“设置生成”按钮：

    ![“设置生成”按钮](media/cicd/vsts-setup-build.png)

1. 从“选择源”部分中选择“GitHub”选项 ：

    ![选择源 - GitHub](media/cicd/vsts-select-source.png)

1. Azure DevOps 需要经过授权才可访问 GitHub 存储库。 在“连接名称”文本框中输入“<GitHub_username> GitHub 连接”。 例如：

    ![GitHub 连接名称](media/cicd/vsts-repo-authz.png)

1. 如果对 GitHub 帐户启用了双因素身份验证，则需要个人访问令牌。 在这种情况下，单击“使用 GitHub 个人访问令牌授权”链接。 有关帮助，请参阅[官方 GitHub 个人访问令牌创建说明](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。 只需要存储库范围内的权限。 否则，单击“使用 OAuth 授权”按钮。
1. 出现提示时，请登录到你的 GitHub 帐户。 然后，选择“授权”，授予对 Azure DevOps 组织的访问权限。 如果成功，新的服务终结点随之创建。
1. 单击“存储库”按钮旁边的省略号按钮。 从列表中选择“<GitHub_username>/simple-feed-reader”存储库。 单击“选择”按钮。
1. 从“用于手动生成和计划生成的默认分支”下拉列表中选择主分支。 单击“继续”按钮。 此时将显示模板选择页。

### <a name="create-the-build-definition"></a>创建生成定义

1. 在模板选择页的“搜索”框中输入 ASP.NET Core：

    ![模板页上的 ASP.NET Core 搜索](media/cicd/vsts-template-selection.png)

1. 模板搜索结果随即显示。 将鼠标悬停在“ASP.NET Core”模板上，然后单击“应用”按钮 。
1. 生成定义的“任务”选项卡随即出现。 单击“触发器”  选项卡。
1. 单击“启用持续集成”框。 在“分支筛选器”部分下，确认“类型”下拉列表设置为“包括” 。 将“分支规范”下拉列表设置为“主”。

    ![启用持续集成设置](media/cicd/vsts-enable-ci.png)

    这些设置会在将任何更改推送到 GitHub 存储库的主分支时触发生成。 持续集成在[提交对 GitHub 的更改并自动部署到 Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) 一节中进行测试。

1. 单击“保存并排队”按钮，然后选择“保存”选项 ：

    ![“保存”按钮](media/cicd/vsts-save-build.png)

1. 以下模式对话框随即显示：

    ![保存生成定义 - 模式对话框](media/cicd/vsts-save-modal.png)

    使用 \\ 的默认文件夹，然后单击“保存”按钮。

### <a name="create-the-release-pipeline"></a>创建发布管道

1. 单击团队项目的“发布”选项卡。 单击“新建管道”按钮。

    ![“发布”选项卡 -“新建”定义按钮](media/cicd/vsts-new-release-definition.png)

    此时将显示模板选择窗格。

1. 在模板选择页的搜索框中输入应用服务：

    ![发布管道模板搜索框](media/cicd/vsts-release-template-search.png)

1. 模板搜索结果随即显示。 将鼠标悬停在“Azure 应用服务的槽部署”模板上，然后单击“应用”按钮 。 发布管道的“管道”选项卡出现。

    ![发布管道的“管道”选项卡](media/cicd/vsts-release-definition-pipeline.png)

1. 单击“项目”框中的“添加”按钮 。 此时将显示“添加项目”面板：

    ![发布管道 - 添加项目面板](media/cicd/vsts-release-add-artifact.png)

1. 从“源类型”部分选择“生成”磁贴 。 此类型允许将发布管道链接到生成定义。
1. 从“项目”下拉列表中选择“MyFirstProject”。
1. 从“源(生成定义)”下拉列表中选择生成定义名称“MyFirstProject-ASP.NET Core-CI”。
1. 从“默认版本”下拉列表中选择“最新”。 此选项生成由最新运行的生成定义生成的项目。
1. 使用“Drop”替换“源别名”文本框中的文本。
1. 单击“添加”按钮。 “项目”部分将更新以显示更改。
1. 单击闪电图标以启用持续部署：

    ![发布管道的“项目”- 闪电符号图标](media/cicd/vsts-artifacts-lightning-bolt.png)

    启用此选项后，每次有新生成可用时都会进行部署。
1. 右侧随即出现“持续部署触发器”面板。 单击“切换”按钮以启用该功能。 无需启用“拉取请求触发器”。
1. 单击“生成分支筛选器”部分中的“添加”下拉列表 。 选择“生成定义的默认分支”选项。 此筛选器使得发布仅针对来自 GitHub 存储库的主分支的生成而触发。
1. 单击“保存”按钮。 在生成的“保存”模式对话框中，单击“确定”按钮 。
1. 单击“环境 1”框。 右侧随即出现“环境”面板。 将“环境名称”文本框中的“环境 1”文本更改为“生产” 。

   ![发布管道 -“环境名称”文本框](media/cicd/vsts-environment-name-textbox.png)

1. 单击“生产”框中的“1 个阶段、2 个任务”链接 ：

    ![发布管道 - 生产环境链接图像 (.png)](media/cicd/vsts-production-link.png)

    此时将显示环境的“任务”选项卡。
1. 单击“将 Azure 应用服务部署到槽”任务。 其设置显示在右侧的面板中。
1. 从“Azure 订阅”下拉列表中选择与应用服务关联的 Azure 订阅。 选择后，单击“授权”按钮。
1. 从“应用类型”下拉菜单中选择“Web 应用”。
1. 从“应用服务名称”下拉列表中选择“mywebapp/<unique_number/>”。
1. 从“资源组”下拉列表中选择“AzureTutorial”。
1. 从“槽”下拉列表中选择“暂存”。
1. 单击“保存”按钮。
1. 将鼠标悬停在默认的发布管道名称上。 单击铅笔图标进行编辑。 使用“MyFirstProject-ASP.NET Core-CD”作为名称。

    ![发布管道名称](media/cicd/vsts-release-definition-name.png)

1. 单击“保存”按钮。

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>提交对 GitHub 所做的更改并将其自动部署到 Azure

1. 在 Visual Studio 中打开“SimpleFeedReader.sln”。
1. 在解决方案资源管理器中，打开 Pages\Index.cshtml。 将 `<h2>Simple Feed Reader - V3</h2>` 更改为 `<h2>Simple Feed Reader - V4</h2>`。
1. 按 Ctrl+Shift+B 生成应用  。
1. 将该文件提交到 GitHub 存储库。 使用 Visual Studio“团队资源管理器”选项卡中的“更改”页，或者使用本地计算机的命令行界面执行以下操作：

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. 将主分支中的更改推送到 GitHub 存储库的源远程库 ：

    ```console
    git push origin master
    ```

    提交出现在 GitHub 存储库的主分支中：

    ![主分支中的 GitHub 提交](media/cicd/github-commit.png)

    生成被触发，因为在生成定义的“触发器”选项卡中启用了持续集成：

    ![启用持续集成](media/cicd/enable-ci.png)

1. 导航到 Azure DevOps Services 中“Azure Pipelines” > “生成”页面的“已排队”选项卡  。 队列生成显示触发生成的分支和提交：

    ![已排队的生成](media/cicd/build-queued.png)

1. 生成成功后便会部署到 Azure。 在浏览器中导航到应用。 请注意，“V4”文本显示在标题中：

    ![已更新应用](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>检查 Azure Pipelines 管道

### <a name="build-definition"></a>生成定义

已创建名为“MyFirstProject-ASP.NET Core-CI”的生成定义。 完成后，该生成会产生一个包含要发布的资产的 .zip文件。 发布管道将这些资产部署到 Azure。

生成定义的“任务”选项卡列出了所使用的各个步骤。 有五个生成任务。

![生成定义任务](media/cicd/build-definition-tasks.png)

1. 还原 &mdash; 执行 `dotnet restore` 命令，还原应用的 NuGet 包。 使用的默认包源为 nuget.org。
1. 生成 &mdash; 执行 `dotnet build --configuration release` 命令，编译应用的代码。 此 `--configuration` 选项用于生成代码的优化版本，该版本适合部署到生产环境。 例如，如果需要调试配置，请修改生成定义的“变量”选项卡上的“BuildConfiguration”变量。
1. 测试 &mdash; 执行 `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 命令，运行应用的单元测试。 单元测试在与 `**/*Tests/*.csproj` glob 模式匹配的任何 C# 项目中执行。 测试结果保存在位于 `--results-directory` 选项指定的位置的 .trx 文件中。 如果任何测试失败，则生成将失败且不会部署。

    > [!NOTE]
    > 若要验证单元测试是否运作正常，请修改 SimpleFeedReader.Tests\Services\NewsServiceTests.cs，有意地中断其中一个测试。 例如，在 `Returns_News_Stories_Given_Valid_Uri` 方法中，将 `Assert.True(result.Count > 0);` 更改为 `Assert.False(result.Count > 0);`。 提交更改并将其推送到 GitHub。 生成已触发并失败。 生成管道状态更改为“失败”。 还原更改、提交并再次推送。 生成成功。

1. 发布&mdash; 执行 `dotnet publish --configuration release --output <local_path_on_build_agent>` 命令，生成包含要部署的项目的 .zip 文件。 `--output` 选项指定 .zip 文件的发布位置。 通过传递名为 `$(build.artifactstagingdirectory)` 的[预定义变量](/azure/devops/pipelines/build/variables)来指定该位置。 该变量扩展到生成代理上的本地路径，如“c:\agent\_work\1\a”。
1. 发布项目 &mdash; 发布由“发布”任务生成的 .zip 文件 。 任务接受 .zip 文件位置作为参数，这是预定义的变量 `$(build.artifactstagingdirectory)`。 .zip 文件作为名为“drop”的文件夹发布 。

单击生成定义的“摘要”链接，查看具有以下定义的生成的历史记录：

![显示生成定义历史记录的屏幕截图](media/cicd/build-definition-summary.png)

在生成的页面上，单击与唯一生成号对应的链接：

![显示生成定义摘要页的屏幕截图](media/cicd/build-definition-completed.png)

随即显示此特定生成的摘要。 单击“项目”选项卡，便可注意到该生成产生的“drop”文件夹已列出：

![显示生成定义项目 - drop 文件夹的屏幕截图](media/cicd/build-definition-artifacts.png)

使用“下载”和“浏览”链接检查已发布的项目 。

### <a name="release-pipeline"></a>发布管道

已创建名为“MyFirstProject-ASP.NET Core-CD”的发布管道：

![显示发布管道概述的屏幕截图](media/cicd/release-definition-overview.png)

发布管道的两个主要组件是“项目”和“环境” 。 单击“项目”部分中的框将显示以下面板：

![显示发布管道项目的屏幕截图](media/cicd/release-definition-artifacts.png)

“源(生成定义)”值表示此发布管道链接到的生成定义。 成功运行生成定义所生成的 .zip 文件将提供给“生产”环境以部署到 Azure 。 单击“生产”环境框中的“1 个阶段、2 个任务”链接，查看发布管道任务 ：

![显示发布管道任务的屏幕截图](media/cicd/release-definition-tasks.png)

发布管道包含两个任务：“将 Azure 应用服务部署到槽”和“管理 Azure 应用服务 - 槽交换” 。 单击第一个任务将显示以下任务配置：

![显示发布管道部署任务的屏幕截图](media/cicd/release-definition-task1.png)

Azure 订阅、服务类型、Web 应用名称、资源组和部署槽位在部署任务中定义。 “包或文件夹”文本框保存要提取并部署到 mywebapp\<unique_number\> Web 应用“暂存”槽的 .zip 文件路径。  

单击槽交换任务将显示以下任务配置：

![显示发布管道槽交换任务的屏幕截图](media/cicd/release-definition-task2.png)

订阅、资源组、服务类型、Web 应用名称和部署槽位的详细信息已提供。 “与生成交换”复选框已选中。 因此，部署到“暂存”槽的位将交换到生产环境中。

## <a name="additional-reading"></a>其他阅读材料

* [使用 Azure Pipelines 创建你的第一个管道](/azure/devops/pipelines/get-started-yaml)
* [生成和 .NET Core 项目](/azure/devops/pipelines/languages/dotnet-core)
* [使用 Azure Pipelines 部署 Web 应用](/azure/devops/pipelines/targets/webapp)
