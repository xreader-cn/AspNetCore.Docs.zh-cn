---
uid: mvc/overview/getting-started/database-first-development/setting-up-database
title: 教程：开始使用 EF Database First 通过 MVC 5
description: 本教程介绍如何开始使用现有数据库并快速创建 web 应用程序，使用户能够与数据交互。
author: Rick-Anderson
ms.author: riande
ms.date: 01/15/2019
ms.topic: tutorial
ms.assetid: 095abad4-3bfe-4f06-b092-ae6a735b7e49
msc.legacyurl: /mvc/overview/getting-started/database-first-development/setting-up-database
msc.type: authoredcontent
ms.openlocfilehash: d99fdb5382037038d4428ff1946f39aee380fb75
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248220"
---
# <a name="tutorial-get-started-with-ef-database-first-using-mvc-5"></a>教程：开始使用 EF Database First 通过 MVC 5

使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。 本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。 生成的代码对应于数据库表中的列。 在本系列的最后一个部分，介绍如何将数据批注添加到数据模型以指定验证要求和显示格式。 完成后，你可以转到 Azure 的文章，了解如何将.NET 应用程序和 SQL 数据库部署到 Azure 应用服务。

本教程介绍如何开始使用现有数据库并快速创建 web 应用程序，使用户能够与数据交互。 它使用 Entity Framework 6 和 MVC 5 构建 web 应用程序。 ASP.NET 基架功能可以自动生成用于显示、 更新、 创建和删除数据的代码。 使用 Visual Studio 中的发布工具，你可以轻松部署站点和数据库到 Azure。

本系列的此部分主要介绍在创建数据库和使用数据填充。

这一系列已用的发布内容写入 Tom Dykstra 和 Rick Anderson。 它改进了基于注释部分中的用户的反馈。

在本教程中，你将了解：

> [!div class="checklist"]
> * 将数据库设置

## <a name="prerequisites"></a>系统必备

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)


## <a name="set-up-the-database"></a>将数据库设置

以模拟拥有现有的数据库的环境，你将首先使用一些预填充的数据，创建一个数据库，然后创建 web 应用程序连接到数据库。


本教程是使用 Visual Studio 2017 使用 LocalDB 开发的。 您可以使用现有的数据库服务器，而不是 LocalDB，但具体取决于版本的 Visual Studio 和您的数据库的类型，所有 Visual Studio 中的数据工具可能不受支持。 如果这些工具不可用于你的数据库，您可能需要执行某些管理套件中的特定于数据库的步骤为你的数据库。


如果你的 Visual Studio 版本中有数据库工具的问题，请确保已安装的数据库工具的最新版本。 有关更新或安装数据库工具的信息，请参阅[Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/hh297027)。

启动 Visual Studio 并创建**SQL Server 数据库项目**。 将项目命名**ContosoUniversityData**。

![创建数据库项目](setting-up-database/_static/image1.png)

您现在有一个空数据库项目。 若要确保可以将此数据库部署到 Azure，您将为该项目的目标平台设置 Azure SQL 数据库。 设置目标平台不实际部署该数据库。它只意味着数据库项目将验证数据库设计为与目标平台兼容。 若要设置目标平台，打开**属性**项目并选择**Microsoft Azure SQL 数据库**为目标平台。

可以创建本教程中添加的定义的表的 SQL 脚本所需的表。 右键单击项目并添加新项。 选择**表和视图** > **表**并将其命名*学生*。

表文件中替换以下代码以创建表的 T-SQL 命令。

[!code-sql[Main](setting-up-database/samples/sample1.sql)]

请注意，设计窗口会自动同步的代码。 您可以使用代码或设计器。

![显示代码和设计](setting-up-database/_static/image5.png)

添加另一个表。 这一次课程将其命名，并使用以下 T-SQL 命令。

[!code-sql[Main](setting-up-database/samples/sample2.sql)]

然后，重复一次，以创建名为注册的表。

[!code-sql[Main](setting-up-database/samples/sample3.sql)]

您可以在用数据填充数据库通过部署数据库后运行的脚本。 向项目添加后期部署脚本。 右键单击项目并添加新项。 选择**用户脚本** > **后期部署脚本**。 可以使用默认名称。

将以下 T-SQL 代码添加到后期部署脚本。 此脚本只需将数据添加到数据库时找到没有匹配的记录。 不覆盖或删除您可能会输入到数据库的任何数据。

[!code-sql[Main](setting-up-database/samples/sample4.sql)]

请务必注意每次部署您的数据库项目时，运行后期部署脚本。 因此，您需要编写此脚本时请仔细考虑您的要求。 在某些情况下，你可能想要从一组已知的数据开始，每次部署该项目。 在其他情况下，您可能不想要更改的现有数据以任何方式。 根据您的要求，可以决定是否需要后期部署脚本或需要在脚本中包括。 有关填充使用后期部署脚本在数据库的详细信息，请参阅[SQL Server 数据库项目中包括数据](https://blogs.msdn.com/b/ssdt/archive/2012/02/02/including-data-in-an-sql-server-database-project.aspx)。

现在，您有 4 个 SQL 脚本文件不存在实际的表。 已准备好将您的数据库项目部署到 localdb。 在 Visual Studio 中，单击开始按钮 （或 F5） 以生成和部署数据库项目。 检查**输出**选项卡以验证生成和部署是否成功。

若要查看已创建新数据库，请打开**SQL Server 对象资源管理器**并查找正确的本地数据库服务器中的项目的名称 (在这种情况下 **(localdb) \ProjectsV13**)。

若要查看表中填充了数据，请右键单击某个表，并选择**查看数据**。

![显示表数据](setting-up-database/_static/image9.png)

显示表数据的可编辑视图。 例如，如果您选择**表** > **dbo.course** > **查看数据**，请参阅具有三个列的表 (**课程**，**标题**，和**信用额度**) 和四个行。

## <a name="additional-resources"></a>其他资源

Code First 开发的介绍性示例，请参阅[Getting Started with ASP.NET MVC 5](../introduction/getting-started.md)。 有关更高级的示例，请参阅[为 ASP.NET MVC 4 应用程序创建实体框架数据模型](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)。

选择要使用的实体框架方法的指导，请参阅[实体框架开发方法](https://msdn.microsoft.com/library/ms178359.aspx#dbfmfcf)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 将数据库设置

转到下一步的教程，了解如何创建 web 应用程序和数据模型。
> [!div class="nextstepaction"]
> [创建 web 应用程序和数据模型](creating-the-web-application.md)
