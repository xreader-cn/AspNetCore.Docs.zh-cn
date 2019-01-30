---
uid: mvc/overview/getting-started/database-first-development/creating-the-web-application
title: 教程：创建的 Web 应用程序和 ef 数据模型数据库优先使用 ASP.NET MVC
description: 本教程重点介绍创建 web 应用程序，并生成基于数据库表的数据模型。
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: bc8f2bd5-ff57-4dcd-8418-a5bd517d8953
msc.legacyurl: /mvc/overview/getting-started/database-first-development/creating-the-web-application
msc.type: authoredcontent
ms.openlocfilehash: dced55386c3f810e406c5c2b3f0071b45e3b2dbd
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236362"
---
# <a name="tutorial-create-the-the-web-application-and-data-models-for-ef-database-first-with-aspnet-mvc"></a>教程：创建的 Web 应用程序和 ef 数据模型数据库优先使用 ASP.NET MVC

 使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。 本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。 生成的代码对应于数据库表中的列。

本教程重点介绍创建 web 应用程序，并生成基于数据库表的数据模型。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 ASP.NET Web 应用
> * 生成模型

## <a name="prerequisites"></a>系统必备

* [使用 Entity Framework 6 Database First 通过 MVC 5 入门](setting-up-database.md)

## <a name="create-an-aspnet-web-app"></a>创建 ASP.NET Web 应用

在新的解决方案或与数据库项目相同的解决方案中，创建 Visual Studio 中的新项目，然后选择**ASP.NET Web 应用程序**模板。 将项目命名**ContosoSite**。

![创建项目](creating-the-web-application/_static/image1.png)

单击 **“确定”**。

在新建 ASP.NET 项目窗口中，选择**MVC**模板。 您可以清除**在云中托管**现在选项，因为你将部署到云的应用程序更高版本。 单击**确定**创建应用程序。

使用默认文件和文件夹创建项目。

在本教程中，将使用 Entity Framework 6。 可以通过管理 NuGet 包窗口在项目中，请仔细检查 Entity Framework 的版本。 如果有必要，请更新你的实体框架版本。

![显示版本](creating-the-web-application/_static/image3.png)

## <a name="generate-the-models"></a>生成模型

现在将从数据库表创建实体框架模型。 这些模型是将用于处理的数据的类。 每个模型反映数据库中的表，并包含对应于表中的列的属性。

右键单击**模型**文件夹，然后选择**添加**并**新项**。

在添加新项窗口中，选择**数据**的左窗格中并**ADO.NET 实体数据模型**从中间窗格中的选项。 将新的模型文件**ContosoModel**。

单击 **添加**。

在实体数据模型向导中，选择**EF 设计器从数据库**。

单击 **“下一步”**。

如果必须在开发环境中定义的数据库连接，可能会看到其中一个预先选择这些连接。 但是，你想要创建新的连接到在本教程的第一部分创建的数据库。 单击**新的连接**按钮。

在连接属性窗口中，提供了创建数据库时所在的本地服务器的名称 (在这种情况下 **(localdb) \Projects13**)。 提供服务器名称后, 从可用数据库中选择 ContosoUniversityData。

![设置连接属性](creating-the-web-application/_static/image8.png)

单击 **“确定”**。

此时将显示正确的连接属性。 可以在 Web.Config 文件中使用连接的默认名称。

单击 **“下一步”**。

选择实体框架的最新版本。

单击 **“下一步”**。

选择**表**来生成模型的所有三个表。

单击 **“完成”**。

如果你收到一条安全警告，选择**确定**继续运行模板。

从数据库表生成模型和关系图显示，其中显示的属性和表之间的关系。

![模型的关系图](creating-the-web-application/_static/image11.png)

模型文件夹现在包括许多与从数据库生成的模型相关的新文件。

**ContosoModel.Context.cs**文件包含的类的派生自**DbContext**类，并提供了用于每个对应于数据库表的 model 类属性。 **Course.cs**， **Enrollment.cs**，并**Student.cs**文件包含表示数据库表的模型类。 使用基架时，将使用上下文类和模型类。

本教程前，生成项目。 在下一步部分中，将生成基于数据模型代码，但如果未生成项目，将无法工作部分。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 ASP.NET web 应用
> * 生成模型

转到下一步的教程，了解如何创建生成基于数据模型的代码。
> [!div class="nextstepaction"]
> [生成视图](generating-views.md)