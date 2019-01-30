---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: 教程：自定义视图的 EF 数据库第一个与 ASP.NET MVC 应用
description: 本教程重点介绍更改的自动生成的视图，以增强此演示文稿。
author: Rick-Anderson
ms.author: riande
ms.date: 01/24/2019
ms.topic: tutorial
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: 89b8a0eb84b6e287c45bc141c68a2c76e63b0e41
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236492"
---
# <a name="tutorial-customize-view-for-ef-database-first-with-aspnet-mvc-app"></a>教程：自定义视图的 EF 数据库第一个与 ASP.NET MVC 应用

使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。 本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。 生成的代码对应于数据库表中的列。

本教程重点介绍更改的自动生成的视图，以增强此演示文稿。

在本教程中，你将了解：

> [!div class="checklist"]
> * 将课程添加到学生详细信息页
> * 确认课程都添加到页面

## <a name="prerequisites"></a>系统必备

* [更改数据库](changing-the-database.md)

## <a name="add-courses-to-student-detail"></a>将课程添加到学生详细信息

生成的代码为应用程序提供很好的起点，但它不一定提供全部所需应用程序中的功能。 可以自定义代码来满足你的应用程序的特定要求。 目前，你的应用程序不显示所选学生的已注册的课程。 在本部分中，您将添加已注册的课程到每个学生**详细信息**学生的视图。

打开**视图** > **学生** > *Details.cshtml*。 最后一个下面&lt;/dl&gt;标记，但在关闭前&lt;/div&gt;标记中，添加以下代码。

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

此代码将创建所选学生 Enrollment 表中显示为每个记录的行的表。 **显示**方法表示表达式的对象 (modelItem) 将呈现 HTML。 使用 Display 方法 （而不是只需在代码中嵌入的属性值） 以确保设置的值格式正确基于其类型和该类型的模板。 在此示例中，从当前记录在循环中，每个表达式返回的单个属性和值是基元类型的呈现为文本。

## <a name="confirm-courses-are-added"></a>确认也会添加课程

运行该解决方案。 单击**学生列表**，然后选择**详细信息**一个学生。 你将看到在视图中包含了已注册的课程。

![与注册的学生](customizing-a-view/_static/image1.png)

## <a name="next-steps"></a>后续步骤
在本教程中，你将了解：

> [!div class="checklist"]
> * 添加了的课程为学生详细信息页
> * 确认课程都添加到页面

转到下一步的教程，了解如何添加数据批注以指定验证要求和显示格式。
> [!div class="nextstepaction"]
> [增强数据验证](enhancing-data-validation.md)
