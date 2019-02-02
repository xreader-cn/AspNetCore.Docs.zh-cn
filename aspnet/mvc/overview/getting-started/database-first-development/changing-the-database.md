---
uid: mvc/overview/getting-started/database-first-development/changing-the-database
title: 教程：更改数据库的 EF 数据库第一个与 ASP.NET MVC 应用
description: 本教程重点介绍对数据库结构进行更新并传播整个 web 应用程序所做的更改。
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: cfd5c083-a319-482e-8f25-5b38caa93954
msc.legacyurl: /mvc/overview/getting-started/database-first-development/changing-the-database
msc.type: authoredcontent
ms.openlocfilehash: 52cad1120908cf0d4f85770f8e2690f9415c5f56
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667630"
---
# <a name="tutorial-change-the-database-for-ef-database-first-with-aspnet-mvc-app"></a>教程：更改数据库的 EF 数据库第一个与 ASP.NET MVC 应用

使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。 本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。 生成的代码对应于数据库表中的列。

本教程重点介绍对数据库结构进行更新并传播整个 web 应用程序所做的更改。

在本教程中，你将了解：

> [!div class="checklist"]
> * 添加列
> * 将属性添加到视图的视图

## <a name="prerequisites"></a>系统必备

* [生成视图](generating-views.md)

## <a name="add-a-column"></a>添加列

如果在数据库中更新的表的结构，您需要确保所做的更改将传播到数据模型、 视图和控制器。

对于本教程，将向 Student 表来记录学生的中间名来添加新列。 若要添加此列，请打开数据库项目中，并打开 Student.sql 文件。 通过在设计器或 T-SQL 代码中，添加名为的列**MiddleName** nvarchar （50） 且允许 NULL 值。

将此更改部署到您的本地数据库中，通过启动您的数据库项目 （或 F5）。 新字段添加到表。 如果您没有看到它在 SQL Server 对象资源管理器中，单击窗格中的刷新按钮。

![显示新的列](changing-the-database/_static/image2.png)

新的列存在在数据库表中，但它当前不存在的数据模型类中。 必须更新以包括新列的模型。 在中**模型**文件夹中，打开**ContosoModel.edmx**文件以显示模型关系图。 请注意，学生模型不包含 MiddleName 属性。 右键单击设计图面上的任意位置，然后选择**从数据库更新模型**。

在更新向导中，选择**刷新**选项卡，然后选择**表** > **dbo** > **学生**。 单击 **“完成”**。

更新过程完成后，数据库关系图中包括的新**MiddleName**属性。 保存**ContosoModel.edmx**文件。 您必须将此文件保存为新的属性传播到**Student.cs**类。 您现在已更新数据库和模型。

生成解决方案。

## <a name="add-the-property-to-the-views"></a>将属性添加到视图的视图

遗憾的是，视图仍会包含新属性。 若要更新的视图有两个选项-可以重新生成视图通过再一次添加基架 Student 类，或可以手动向现有视图添加新属性。 在本教程中，您将添加基架再次因为不到自动生成的视图进行了任何自定义的更改。 您可以考虑手动将属性添加到视图的视图进行了更改并不希望丢失这些更改时。

若要确保已重新创建视图，请删除**学生**下的文件夹**视图**，并删除**StudentsController**。 然后，右键单击**控制器**文件夹，并添加基架**学生**模型。 同样，将控制器命名**StudentsController**。 选择“添加”。

重新生成解决方案。 视图现在包含 MiddleName 属性。

![显示中间名](changing-the-database/_static/image5.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 添加列
> * 将属性添加到视图的视图

转到下一步的教程，了解如何自定义用于显示有关学生记录的详细信息视图。
> [!div class="nextstepaction"]
> [自定义视图](customizing-a-view.md)