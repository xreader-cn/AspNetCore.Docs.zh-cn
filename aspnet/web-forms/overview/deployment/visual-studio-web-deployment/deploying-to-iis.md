---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
title: 使用 Visual Studio 的 ASP.NET Web 部署：部署到测试 |Microsoft Docs
author: tdykstra
description: 本系列教程演示如何部署 （发布） ASP.NET web 应用程序到 Azure 应用服务 Web 应用或第三方托管提供商，通过使用...
ms.author: riande
ms.date: 01/16/2019
ms.assetid: 8bf2c4fb-4ee5-4841-bfc2-03462c1f7a7a
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
msc.type: authoredcontent
ms.openlocfilehash: d49dfad368ca4b81bb865103a99ec223a1cc66df
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396332"
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-to-test"></a>使用 Visual Studio 的 ASP.NET Web 部署：部署到测试
====================
通过[Tom Dykstra](https://github.com/tdykstra)

> 本系列教程演示如何部署 （发布） ASP.NET web 应用程序到 Azure 应用服务 Web 应用或第三方托管提供商使用 Visual Studio 2017。 有关序列的信息，请参阅[系列中的第一个教程](introduction.md)。

## <a name="overview"></a>概述

在本教程中，你将在本地计算机上部署 ASP.NET web 应用程序到 Internet 信息服务器 (IIS)。

通常开发的应用程序，您将运行它并在 Visual Studio 中进行测试。 默认情况下，在 Visual Studio 2017 中的 web 应用程序项目使用 IIS Express 用作开发 web 服务器。 IIS Express 的行为更类似于比 Visual Studio 开发服务器 (也称为 Cassini)，Visual Studio 2017 默认使用的完整 IIS。 但既不开发 web 服务器的工作方式完全相同 IIS。 因此，应用无法运行和测试在 Visual Studio 中的正确但部署到 IIS 时失败。

两种方式，可以可靠地测试你的应用程序：

1. 应用程序部署到 IIS 在开发计算机上使用相同的过程的更高版本将使用它来将其部署到生产环境。

   可以配置 Visual Studio 以使用 IIS 时运行 web 项目中，但不会测试您的部署过程。 此方法验证您的部署过程，并在 IIS 下正确运行你的应用程序。

2. 应用程序部署到测试环境类似于生产环境。 
 
   这些教程中的生产环境是 Azure 应用服务中的 Web 应用。 理想的测试环境是在 Azure 服务中创建的其他 web 应用。 尽管它会设置为生产 web 应用的相同方式，您将仅使用它进行测试。

选项 2 是测试的最可靠方法。 如果使用选项 2，不一定需要使用选项 1。 但是如果要部署到第三方承载提供程序，选项 2 不一定能够使用或可能开销很大，因此本系列教程演示这两种方法。 在提供了选项 2 的指导[部署到生产环境](deploying-to-production.md)教程。

有关在 Visual Studio 中使用 web 服务器的详细信息，请参阅[对于 ASP.NET Web 项目的 Visual Studio 中的 Web 服务器](https://msdn.microsoft.com/library/58wxa9w5.aspx)。

提醒：如果你收到一条错误消息或某些操作无法按完成以下教程，请务必检查[故障排除页](troubleshooting.md)。

## <a name="download-the-contoso-university-starter-project"></a>下载 Contoso University 初学者项目

下载并安装 Contoso 大学 Visual Studio 初学者解决方案和项目。 此解决方案包含已完成本教程。 

[下载初学者项目](http://go.microsoft.com/fwlink/p/?LinkId=282627)

## <a name="install-iis"></a>安装 IIS

若要将部署到 IIS 在开发计算机上，确认安装了 IIS 和 Web 部署。 默认情况下，Visual Studio 会安装 Web 部署，但 IIS 不包括在默认 Windows 10、 Windows 8 或 Windows 7 配置。 如果已安装 IIS 和默认应用程序池已设置为.NET 4，请跳到[下一步部分](#sqlexpress)。

1. 建议你使用[Web 平台安装程序 (WPI)](https://www.microsoft.com/web/downloads/platform.aspx)安装 IIS 和 Web 部署。 WPI 安装建议的 IIS 配置，包括 IIS 和 Web 部署的先决条件，如有必要。

   如果你已安装 IIS、 Web 部署，或任何其所需的组件，则会安装 WPI 只缺少的内容。

   * 使用 Web 平台安装程序安装 IIS 和 Web 部署：
    
     ![使用 WPI 安装 IIS](deploying-to-iis/_static/image30.png)

     ![安装 Web 部署使用 WPI](deploying-to-iis/_static/image31.png)

     可以看到，该值指示将安装 IIS 7 的消息。 适用于 Windows 8; 中的 IIS 8 可使用该链接但 Windows 8 和更高版本，通过以下步骤以确保已安装 ASP.NET 4.7:

   * 打开**Control Panel** > **程序** > **程序和功能** > **打开或关闭打开的 Windows 功能**.

   * 展开**Internet 信息服务**，**万维网服务**，并**应用程序开发功能**。
   
   * 确认**ASP.NET 4.7**处于选中状态。

     ![选择 ASP.NET 4.7](deploying-to-iis/_static/image1a.png)

   * 确认**World Wide Web 服务**并**IIS 管理控制台**处于选中状态。 这会安装 IIS 和 IIS 管理器。
    
     ![选择万维网服务](deploying-to-iis/_static/image24.png)    
  
   * 选择“确定”。 对话框消息，指示安装正在进行显示。

安装 IIS 后，运行**IIS 管理器**以确保.NET Framework 版本 4 分配给默认应用程序池。

1. 按 WINDOWS + R 打开**运行**对话框。

   (在 Windows 8 或更高版本中，输入"运行"的**启动**页。 在 Windows 7 中，选择**运行**从**启动**菜单。 如果**运行**不在**启动**菜单中，右键单击任务栏中，选择**属性**，选择 **「 开始 」 菜单**选项卡上，选择**自定义**，然后选择**运行命令**。)

2. 输入"inetmgr"，然后选择**确定**。

3. 在中**连接**窗格中，展开服务器节点，然后选择**应用程序池**。 在中**应用程序池**窗格如果**DefaultAppPool**是分配给.NET framework 版本 4 所示，跳到下一节。

   ![Inetmgr_showing_4.0_app_pools](deploying-to-iis/_static/image5a.png)

4. 如果您看到只有两个应用程序池，并且两者都设置为.NET Framework 2.0，请在 IIS 中安装 ASP.NET 4。

   Windows 8 或更高版本，请参阅上一部分的说明，确保该 ASP.NET 4.7 安装或请参阅[如何在 Windows 8 和 Windows Server 2012 上安装 ASP.NET 4.5](https://support.microsoft.com/kb/2736284)。 对于 Windows 7 中，打开命令提示符窗口，请右键单击**命令提示符**在 Windows 中**启动**菜单并选择**以管理员身份运行**。 运行[aspnet\_regiis.exe](https://msdn.microsoft.com/library/k6h9cz8h.aspx)中 IIS，使用以下命令安装 ASP.NET 4。 （在 32 位系统中，将为"Framework64"与"框架"。）

   [!code-console[Main](deploying-to-iis/samples/sample1.cmd)]

   此命令创建新的应用程序池的.NET Framework 4 中，但默认应用程序池将保持设置为 2.0。 要部署应用程序以.NET 4 为目标为该应用程序池，因此为.NET 4 更改应用程序池。

5. 如果您关闭**IIS 管理器**，再次运行中，展开服务器节点，并选择**应用程序池**。

6. 在中**应用程序池**窗格中，选择**DefaultAppPool**。 在中**操作**窗格中，选择**基本设置**。

   ![Inetmgr_selecting_Basic_Settings_for_app_pool](deploying-to-iis/_static/image25.png)

7. 在中**编辑应用程序池**对话框中，更改 **.NET CLR 版本**到 **.NET CLR v4.0.30319**。 选择“确定”。

   ![Selecting_.NET_4_for_DefaultAppPool](deploying-to-iis/_static/image6a.png)

现在你准备好发布到 IIS 的 web 应用程序。 首先，但是，创建用于测试的数据库。

<a id="sqlexpress"></a>

## <a name="install-sql-server-express"></a>安装 SQL Server Express

LocalDB 不被设计用于处理在 IIS 中，因此你的测试环境必须有 SQL Server Express 安装。 如果使用 Visual Studio 2010 SQL Server Express，它是已安装默认情况下。 如果你使用 Visual Studio 2012 或更高版本，安装 SQL Server Express。

若要安装 SQL Server Express，下载并安装它从[下载中心：Microsoft SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express)。 

SQL Server 安装中心的第一页，选择**新的 SQL Server 独立安装或向现有安装添加功能**并按照说明接受默认选项。 在安装向导中，接受默认设置。 有关安装选项的详细信息，请参阅[从安装向导 （安装程序） 安装 SQL Server](https://msdn.microsoft.com/library/ms143219.aspx)。

## <a name="create-sql-server-express-databases-for-the-test-environment"></a>创建 SQL Server Express 数据库的测试环境

Contoso 大学应用程序有两个数据库： 

1. 成员资格数据库 
2. 应用程序数据库 

可以将这些数据库部署到两个独立数据库或单个数据库。 将它们合并，使它们更容易的数据库连接。 

如果你正在部署到第三方托管提供商，托管计划可能还为您提供原因要将它们合并。 例如，提供程序可能会收取多个数据库的详细信息，或可能甚至不允许多个数据库。

在本教程中，你将部署到测试环境中的两个数据库和过渡和生产环境中的一个数据库。

从**视图**在 Visual Studio 中，选择菜单**服务器资源管理器**(**数据库资源管理器**Visual Web Developer 中)。 右键单击**数据连接**，然后选择**创建新的 SQL Server 数据库**。

![Selecting_Create_New_SQL_Server_Database](deploying-to-iis/_static/image8.png)

在中**创建新的 SQL Server 数据库**对话框框中，输入"。 \SQLExpress"在**服务器名称**框和"aspnet-ContosoUniversity"中**新的数据库名称**框。 选择“确定”。

![创建 aspnet ContosoUniversity](deploying-to-iis/_static/image9.png)

请按照相同的过程来创建一个名为的新 SQL Server Express School 数据库`ContosoUniversity`。

**服务器资源管理器**显示了两个新数据库。

![在服务器资源管理器中的新数据库](deploying-to-iis/_static/image10.png)

## <a name="create-a-grant-script-for-the-new-databases"></a>创建新的数据库授予脚本

在开发计算机上在 IIS 中运行的应用程序，该应用程序使用默认应用程序池的凭据来访问数据库。 但是，默认情况下，应用程序池没有打开数据库的权限。 这意味着您需要运行脚本，以授予该权限。 在本部分中，将创建该脚本并运行更高版本以确保在 IIS 中运行时，该应用程序可以打开数据库。

在文本编辑器中，将以下 SQL 命令复制到新文件并将其保存为*Grant.sql*。 

[!code-sql[Main](deploying-to-iis/samples/sample2.sql)]

在 Visual Studio 中，打开 Contoso University 解决方案。 右键单击该解决方案 （而不是项目的），然后选择**添加**。 选择**现有项**，浏览到*Grant.sql*，并将其打开。

> [!NOTE]
> 此脚本在本教程中指定了被设计适用于 SQL Server Express 2012 或更高版本和 Windows 10、 Windows 8 或 Windows 7 中的 IIS 设置。 如果在使用不同版本的 SQL Server 或 Windows，或者如果设置了 IIS 计算机上以不同的方式，可能需要对此脚本的更改。 有关 SQL Server 脚本详细信息，请参阅[SQL Server 联机丛书](https://go.microsoft.com/fwlink/?LinkId=132511)。

> [!NOTE] 
> **安全说明**此脚本将向提供`db_owner`在运行时，这是您在生产环境中必须访问数据库的用户的权限。 在某些情况下，你可能想要指定具有完整的数据库架构更新部署权限和指定的运行时具有的权限仅读取和写入数据的不同用户的用户。 有关详细信息，请参阅[评审 Code First 迁移自动 Web.config 更改](#reviewingmigrations)在本教程中更高版本。

<a id="publish"></a>

## <a name="run-the-grant-script-in-the-application-database"></a>运行应用程序数据库中授予脚本

可以配置要运行授予脚本成员资格数据库中，在部署期间因为该数据库的部署使用的发布配置文件[dbDacFx](https://docs.microsoft.com/iis/publish/using-web-deploy/dbdacfx-provider-for-incremental-database-publishing)提供程序。 在 Code First 迁移部署，这是你要如何部署应用程序数据库期间，无法运行脚本。 这意味着您必须手动运行应用程序数据库中的部署前的脚本。

1. 在 Visual Studio 中打开*Grant.sql*前面创建的文件。

2. 选择**连接**。 

    ![连接按钮](deploying-to-iis/_static/image11.png)

3. 在中**连接到服务器**对话框框中，输入 *。 \SQLExpress*作为**服务器名称**。 选择**连接**。

4. 在数据库下拉列表中选择**ContosoUniversity**。 选择**执行**。 

   ![](deploying-to-iis/_static/image12.png)

现在，默认应用程序池标识代码优先迁移来创建数据库表，该应用程序运行时在应用程序数据库中具有足够的权限。

## <a name="publish-to-iis"></a>发布到 IIS

有几种方法可以将它们部署到 IIS，使用 Visual Studio 和 Web 部署：

* 使用一键式发布的 Visual Studio。
* 从命令行发布。
* 创建部署包并将其使用 IIS 管理器安装。 包具有的所有文件和在 IIS 中安装站点所需的元数据的.zip 文件。
* 创建部署包并将其使用命令行安装。

已在前面的教程，要将 Visual Studio 设置为自动执行部署任务适用于所有这些方法的过程。 在这些教程中，将使用前两个方法。 有关使用部署包的信息，请参阅[web 应用程序部署通过创建和安装 web 部署包](https://go.microsoft.com/fwlink/p/?LinkId=282413#package)for Visual Studio 和 ASP.NET Web 部署内容映射中。

发布前，请确保您要在管理员模式下运行 Visual Studio。 如果没有看到 **（管理员）** 在标题栏中，关闭 Visual Studio。 在 Windows 8 （或更高版本）**启动**页上或 Windows 7**启动**菜单中，右键单击 Visual Studio 图标，然后选择**以管理员身份运行**。 管理员模式下才发布你的本地计算机上发布到 IIS 时所需。

### <a name="create-the-publish-profile"></a>创建发布配置文件

1. 在中**解决方案资源管理器**，右键单击**ContosoUniversity**项目 (不**ContosoUniversity.DAL**项目)。 选择“发布”。 **发布**页将出现。

2. 选择**新的配置文件**。 **选取发布目标**对话框随即出现。

3. 选择**IIS、 FTP 等**。选择“创建配置文件”。 **发布**向导显示。

   ![发布 Web 向导配置文件选项卡](deploying-to-iis/_static/image26.png)

4. 从**发布方法**下拉列表菜单中，选择**Web 部署**。

5. 有关**服务器**，输入*localhost*。

6. 有关**站点名称**，输入*默认 Web 站点/ContosoUniversity*。

7. 有关**目标 URL**，输入*http://localhost/ContosoUniversity*。

   **目标 URL**设置不需要。 完成 Visual Studio 部署应用程序后，它会自动打开默认浏览器访问此 URL。 如果不想要在部署后自动打开浏览器，将此框留空。

8. 选择**验证连接**以验证是否设置正确无误，并可以在本地计算机上连接到 IIS。

   绿色复选标记验证连接成功。

   ![发布 Web 向导连接选项卡](deploying-to-iis/_static/image27.png)

9. 选择**下一步**转到**设置**选项卡。

10. **配置**下拉列表框指定要部署的生成配置。 将保留该设置的默认值为**版本**。 不会将在本教程中部署了调试版本。

11. 展开**文件发布选项**。 选择**将应用程序中排除文件\_数据文件夹**。

    在测试环境中，该应用程序访问本地 SQL Server Express 实例，不在的.mdf 文件中创建的数据库*应用程序\_数据*文件夹。

12. 将保留**在发布期间预编译**并**删除目标处的其他文件**清除复选框。

    ![设置选项卡中的文件发布选项](deploying-to-iis/_static/image15a.png)

    预编译是主要用于大型站点的选项。 它可以减少启动时间后将站点发布请求页的第一个时间。

    不需要由于这是你第一次部署，也不会有任何文件的目标文件夹中尚未删除的其他文件。

    > [!NOTE] 
    > 如果选择**删除目标处的其他文件**对于到相同站点的后续部署，请确保你使用的预览功能，以便你提前看到在部署之前，将删除哪些文件。 预期的行为是，Web 部署会删除已在项目中删除目标服务器上的文件。 但是，在源和目标文件夹下的整个文件夹结构进行比较;并在某些情况下，Web 部署可能会删除不想要删除的文件。
    > 
    > 例如在服务器上的子文件夹中没有的 web 应用程序项目部署到的根文件夹时，如果将删除子文件夹。 你可能在 contoso.com 的主站点的一个项目，并在 contoso.com/blog 博客的另一个项目。 博客应用程序是一个子文件夹中。 如果选择**删除目标处的其他文件**博客应用程序时部署主站点时，将被删除。
    > 
    > 有关其他示例，您的应用程序\_数据文件夹可能会意外删除。 某些数据库，例如 SQL Server Compact 数据库文件存储在应用\_数据文件夹。 在初始部署之后不想要保留复制的数据库文件在后续部署中，因此选择**排除应用\_数据**打包/发布 Web 选项卡上。在执行操作，如果之后有**删除目标处的其他文件**选择，将数据库文件和应用\_的下次发布时都将删除数据文件夹本身。

### <a name="configure-deployment-for-the-membership-database"></a>配置成员资格数据库部署

以下步骤适用于**DefaultConnection**对话框框中的数据库**数据库**部分。

1. 在中**远程连接字符串**框中，输入以下指向新的 SQL Server Express 成员资格数据库的连接字符串。

   [!code-console[Main](deploying-to-iis/samples/sample3.cmd)]

   部署过程： 将此连接字符串放入部署的 Web.config 文件，因为**在运行时使用此连接字符串**处于选中状态。

    此外可以获取的连接字符串**服务器资源管理器**。 在中**服务器资源管理器**，展开**数据连接**，然后选择 **&lt;machinename&gt;\sqlexpress.aspnet-ContosoUniversity**数据库，然后从**属性**窗口中复制**连接字符串**值。 连接字符串将具有一个可以删除的其他设置： `Pooling=False`。

2. 选择**更新数据库**。

   这会导致要在部署过程中创建目标数据库中的数据库架构。 在下一步骤中，指定您需要运行其他脚本： 一个用于授予对默认应用程序池，一个用于部署数据的数据库访问权限。

3. 选择**配置数据库更新**。
 
4. 在中**配置数据库更新**对话框中，选择**添加 SQL 脚本**。 导航到*Grant.sql*之前保存在解决方案文件夹中的脚本。

5. 重复此过程以添加*aspnet 数据 dev.sql*脚本。

   ![为成员资格数据库配置数据库更新](deploying-to-iis/_static/image16.png)

6. 选择**关闭**。

### <a name="configure-deployment-for-the-application-database"></a>配置应用程序数据库的部署

当 Visual Studio 检测到实体框架`DbContext`类，它创建中的条目**数据库**节，其中包含**执行 Code First 迁移**复选框，而不是**更新数据库**复选框。 对于本教程，将使用该复选框来指定 Code First 迁移部署。

在某些情况下，你可能使用`DbContext`数据库，但想要使用 dbDacFx 提供程序而不是迁移部署数据库。 在这种情况下，请参阅[如何将 Code First 数据库而无需迁移的部署？](https://msdn.microsoft.com/library/ee942158.aspx#deploy_code_first_without_migrations) MSDN 上的 ASP.NET Web 部署常见问题解答中。

以下步骤适用于**SchoolContext**对话框框中的数据库**数据库**部分。

1. 在中**远程连接字符串**框中，输入以下指向新的 SQL Server Express 应用程序数据库的连接字符串。

   [!code-console[Main](deploying-to-iis/samples/sample4.cmd)]

   部署过程： 将此连接字符串放入部署的 Web.config 文件，因为**在运行时使用此连接字符串**处于选中状态。

   此外可以获取的应用程序数据库连接字符串**服务器资源管理器**相同的方式获取成员资格数据库连接字符串。

2. 选择**执行 Code First 迁移 （应用程序启动时运行）**。

   此选项将导致部署过程，配置已部署的 Web.config 文件，以指定`MigrateDatabaseToLatestVersion`初始值设定项。 此初始值设定项自动更新数据库的最新版本时该应用程序部署后首次访问数据库。

### <a name="configure-publish-profile-transforms"></a>配置发布配置文件转换

1. 选择**关闭**。 选择**是**当系统询问你是否要保存更改。

2. 在中**解决方案资源管理器**，展开**属性**，展开**PublishProfiles**。

3. 右键单击*CustomProfile.pubxml*并将其重命名*Test.pubxml*。

4. 右键单击*Test.pubxml*。 选择**添加配置转换**。

   ![添加配置转换菜单](deploying-to-iis/_static/image17.png)

   Visual Studio 将创建*Web.Test.config*转换文件并将其打开。

5. 在中*Web.Test.config*转换文件中，打开后立即插入以下代码配置标记。

   [!code-xml[Main](deploying-to-iis/samples/sample5.xml)]

    当使用测试发布配置文件时，此转换设置为"Test"的环境指示器。 在已部署的站点中，将"Contoso University"H1 标题后看到"（测试）"。

6. 保存并关闭文件。

7. 右键单击*Web.Test.config*文件，然后选择**预览版转换**以确保您编码的转换生成预期的更改。

    **Web.config 预览版**窗口中显示结果应用同时*Web.Release.config*转换并*Web.Test.config*转换。

### <a name="preview-the-deployment-updates"></a>预览的部署更新

1. 打开**发布 Web**再次向导 (右键单击 ContosoUniversity 项目，选择**发布**，然后**预览**)。

2. 在中**预览版**对话框中，选择**开始预览**以查看将复制的文件的列表。 

   ![发布预览](deploying-to-iis/_static/image29.png)

   您还可以选择**预览版数据库**链接可查看将在成员资格数据库中运行的脚本。 （没有运行脚本对于 Code First 迁移部署，因此没有要预览的应用程序数据库。）

3. 选择“发布”。

   如果 Visual Studio 不是在管理员模式下，可能会收到权限错误消息。 在这种情况下，关闭 Visual Studio，在管理员模式下打开并尝试再次发布。

   在管理员模式下，Visual Studio 是否**输出**窗口报告成功生成和发布。

   ![Output_window_publish_Test](deploying-to-iis/_static/image20.png)

   如果输入中的 URL**目标 URL**上的发布配置文件框**连接**选项卡上，浏览器会自动打开指向在 IIS 中运行您的计算机上的 Contoso University 主页上。

## <a name="test-in-the-test-environment"></a>在测试环境中测试

请注意，环境指示器，显示"（测试）"而不是"（开发）、"显示的*Web.config*环境指示器的转换是否成功。

运行**讲师**页以验证 Code First 种子讲师数据的数据库。 当选中此页时，可能需要几分钟才能加载，因为 Code First 创建数据库并运行`Seed`方法。 （它没有这样做时，因为该应用程序不尝试尚未访问的数据库都在主页上。）

选择**学生**选项卡以验证是否已部署的数据库具有任何学生。

选择**添加学生**从**学生**菜单。 添加一名学生，，然后查看中的新学生**学生**页。 这将验证可以成功写入到数据库。

从**课程**菜单中，选择**更新信用额度**。 **更新信用额度**页面需要管理员权限，因此**Log In**显示页。 输入创建早期 （"admin"和"devpwd"） 的管理员帐户凭据。 **更新信用额度**显示页。 这将验证在上一教程中创建的管理员帐户已正确部署到测试环境。

确认*ELMAH*文件夹中存在*c:\inetpub\wwwroot\ContosoUniversity*的占位符文件将在其中的文件夹。

<a id="reviewingmigrations"></a>

## <a name="review-the-automatic-webconfig-changes-for-code-first-migrations"></a>查看自动 Web.config 更改的 Code First 迁移

打开*Web.config*文件中的已部署应用程序*C:\inetpub\wwwroot\ContosoUniversity* ，可以看到部署过程会自动配置 Code First 迁移到其中更新到最新版本的数据库。

![](deploying-to-iis/_static/image21.png)

部署过程还创建新的连接字符串的代码优先迁移来更新数据库架构的以独占方式使用：

![Database_Publish 连接字符串](deploying-to-iis/_static/image22.png)

此附加的连接字符串，可指定一个用户帐户对数据库架构更新和应用程序数据访问的其他用户帐户。 例如，可将分配**db\_所有者**到 Code First 迁移角色和**db\_datareader**与**db\_datawriter**向应用程序角色。 这是一种常用的深度防御模式阻止潜在的恶意代码应用程序更改数据库架构。 （例如，可能发生此错误在成功的 SQL 注入式攻击。）这些教程不使用此模式。 若要实现此模式在你的方案中，执行以下步骤：

1. 在中**发布 Web**向导下**设置**选项卡上，输入完整的数据库架构更新权限与指定的用户的连接字符串。 清除**在运行时使用此连接字符串**复选框。 在已部署的 Web.config 文件中，这将成为`DatabasePublish`连接字符串。

2. 创建 Web.config 文件转换为你想要在运行时使用的应用程序的连接字符串。

## <a name="summary"></a>总结

现在已在开发计算机上部署到 IIS 应用程序，并对其进行存在测试。

![在测试中的主页](deploying-to-iis/_static/image23.png)

这将验证部署过程将应用程序的内容复制到正确的位置 （不包括不想部署的文件） 和也该 Web 部署 IIS 正确配置在部署过程。 在下一步的教程中，你将运行一个测试，以发现尚未完成的部署任务： 上设置文件夹权限*Elm ah*文件夹。

## <a name="more-information"></a>详细信息

在 Visual Studio 中运行 IIS 或 IIS Express 的信息，请参阅以下资源：

- [IIS Express 概述](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview)IIS.net 站点上。
- [引入了 IIS Express](https://weblogs.asp.net/scottgu/archive/2010/06/28/introducing-iis-express.aspx) Scott Guthrie 的博客上。
- [Web 服务器在 Visual Studio 中的，对 ASP.NET Web 项目](https://msdn.microsoft.com/library/58wxa9w5.aspx)。
- [核心区别之间 IIS 和 ASP.NET Development Server](../../older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md) ASP.NET 站点上。

在中等信任环境中运行应用程序时，可能出现什么问题的信息，请参阅[承载 ASP.NET 应用程序在中等信任](http://www.4guysfromrolla.com/articles/100307-1.aspx)上 Rolla 站点中的四个专家。

> [!div class="step-by-step"]
> [上一页](project-properties.md)
> [下一页](setting-folder-permissions.md)
