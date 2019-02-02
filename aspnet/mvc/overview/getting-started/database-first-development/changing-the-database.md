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
# <a name="tutorial-change-the-database-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="608f0-103">教程：更改数据库的 EF 数据库第一个与 ASP.NET MVC 应用</span><span class="sxs-lookup"><span data-stu-id="608f0-103">Tutorial: Change the database for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="608f0-104">使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="608f0-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="608f0-105">本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="608f0-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="608f0-106">生成的代码对应于数据库表中的列。</span><span class="sxs-lookup"><span data-stu-id="608f0-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="608f0-107">本教程重点介绍对数据库结构进行更新并传播整个 web 应用程序所做的更改。</span><span class="sxs-lookup"><span data-stu-id="608f0-107">This tutorial focuses on making an update to the database structure and propagating that change throughout the web application.</span></span>

<span data-ttu-id="608f0-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="608f0-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="608f0-109">添加列</span><span class="sxs-lookup"><span data-stu-id="608f0-109">Add a column</span></span>
> * <span data-ttu-id="608f0-110">将属性添加到视图的视图</span><span class="sxs-lookup"><span data-stu-id="608f0-110">Add the property to the views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="608f0-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="608f0-111">Prerequisites</span></span>

* [<span data-ttu-id="608f0-112">生成视图</span><span class="sxs-lookup"><span data-stu-id="608f0-112">Generating views</span></span>](generating-views.md)

## <a name="add-a-column"></a><span data-ttu-id="608f0-113">添加列</span><span class="sxs-lookup"><span data-stu-id="608f0-113">Add a column</span></span>

<span data-ttu-id="608f0-114">如果在数据库中更新的表的结构，您需要确保所做的更改将传播到数据模型、 视图和控制器。</span><span class="sxs-lookup"><span data-stu-id="608f0-114">If you update the structure of a table in your database, you need to ensure that your change is propagated to the data model, views, and controller.</span></span>

<span data-ttu-id="608f0-115">对于本教程，将向 Student 表来记录学生的中间名来添加新列。</span><span class="sxs-lookup"><span data-stu-id="608f0-115">For this tutorial, you will add a new column to the Student table to record the middle name of the student.</span></span> <span data-ttu-id="608f0-116">若要添加此列，请打开数据库项目中，并打开 Student.sql 文件。</span><span class="sxs-lookup"><span data-stu-id="608f0-116">To add this column, open the database project, and open the Student.sql file.</span></span> <span data-ttu-id="608f0-117">通过在设计器或 T-SQL 代码中，添加名为的列**MiddleName** nvarchar （50） 且允许 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="608f0-117">Through either the designer or the T-SQL code, add a column named **MiddleName** that is an NVARCHAR(50) and allows NULL values.</span></span>

<span data-ttu-id="608f0-118">将此更改部署到您的本地数据库中，通过启动您的数据库项目 （或 F5）。</span><span class="sxs-lookup"><span data-stu-id="608f0-118">Deploy this change to your local database by starting your database project (or F5).</span></span> <span data-ttu-id="608f0-119">新字段添加到表。</span><span class="sxs-lookup"><span data-stu-id="608f0-119">The new field is added to the table.</span></span> <span data-ttu-id="608f0-120">如果您没有看到它在 SQL Server 对象资源管理器中，单击窗格中的刷新按钮。</span><span class="sxs-lookup"><span data-stu-id="608f0-120">If you do not see it in the SQL Server Object Explorer, click the Refresh button in the pane.</span></span>

![显示新的列](changing-the-database/_static/image2.png)

<span data-ttu-id="608f0-122">新的列存在在数据库表中，但它当前不存在的数据模型类中。</span><span class="sxs-lookup"><span data-stu-id="608f0-122">The new column exists in the database table, but it does not currently exist in the data model class.</span></span> <span data-ttu-id="608f0-123">必须更新以包括新列的模型。</span><span class="sxs-lookup"><span data-stu-id="608f0-123">You must update the model to include your new column.</span></span> <span data-ttu-id="608f0-124">在中**模型**文件夹中，打开**ContosoModel.edmx**文件以显示模型关系图。</span><span class="sxs-lookup"><span data-stu-id="608f0-124">In the **Models** folder, open the **ContosoModel.edmx** file to display the model diagram.</span></span> <span data-ttu-id="608f0-125">请注意，学生模型不包含 MiddleName 属性。</span><span class="sxs-lookup"><span data-stu-id="608f0-125">Notice that the Student model does not contain the MiddleName property.</span></span> <span data-ttu-id="608f0-126">右键单击设计图面上的任意位置，然后选择**从数据库更新模型**。</span><span class="sxs-lookup"><span data-stu-id="608f0-126">Right-click anywhere on the design surface, and select **Update Model from Database**.</span></span>

<span data-ttu-id="608f0-127">在更新向导中，选择**刷新**选项卡，然后选择**表** > **dbo** > **学生**。</span><span class="sxs-lookup"><span data-stu-id="608f0-127">In the Update Wizard, select the **Refresh** tab and then select **Tables** > **dbo** > **Student**.</span></span> <span data-ttu-id="608f0-128">单击 **“完成”**。</span><span class="sxs-lookup"><span data-stu-id="608f0-128">Click **Finish**.</span></span>

<span data-ttu-id="608f0-129">更新过程完成后，数据库关系图中包括的新**MiddleName**属性。</span><span class="sxs-lookup"><span data-stu-id="608f0-129">After the update process is finished, the database diagram includes the new **MiddleName** property.</span></span> <span data-ttu-id="608f0-130">保存**ContosoModel.edmx**文件。</span><span class="sxs-lookup"><span data-stu-id="608f0-130">Save the **ContosoModel.edmx** file.</span></span> <span data-ttu-id="608f0-131">您必须将此文件保存为新的属性传播到**Student.cs**类。</span><span class="sxs-lookup"><span data-stu-id="608f0-131">You must save this file for the new property to be propagated to the **Student.cs** class.</span></span> <span data-ttu-id="608f0-132">您现在已更新数据库和模型。</span><span class="sxs-lookup"><span data-stu-id="608f0-132">You have now updated the database and the model.</span></span>

<span data-ttu-id="608f0-133">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="608f0-133">Build the solution.</span></span>

## <a name="add-the-property-to-the-views"></a><span data-ttu-id="608f0-134">将属性添加到视图的视图</span><span class="sxs-lookup"><span data-stu-id="608f0-134">Add the property to the views</span></span>

<span data-ttu-id="608f0-135">遗憾的是，视图仍会包含新属性。</span><span class="sxs-lookup"><span data-stu-id="608f0-135">Unfortunately, the views still do not contain the new property.</span></span> <span data-ttu-id="608f0-136">若要更新的视图有两个选项-可以重新生成视图通过再一次添加基架 Student 类，或可以手动向现有视图添加新属性。</span><span class="sxs-lookup"><span data-stu-id="608f0-136">To update the views you have two options - you can either re-generate the views by once again adding scaffolding for the Student class, or you can manually add the new property to your existing views.</span></span> <span data-ttu-id="608f0-137">在本教程中，您将添加基架再次因为不到自动生成的视图进行了任何自定义的更改。</span><span class="sxs-lookup"><span data-stu-id="608f0-137">In this tutorial, you will add the scaffolding again because you have not made any customized changes to the automatically-generated views.</span></span> <span data-ttu-id="608f0-138">您可以考虑手动将属性添加到视图的视图进行了更改并不希望丢失这些更改时。</span><span class="sxs-lookup"><span data-stu-id="608f0-138">You might consider manually adding the property when you have made changes to the views and do not want to lose those changes.</span></span>

<span data-ttu-id="608f0-139">若要确保已重新创建视图，请删除**学生**下的文件夹**视图**，并删除**StudentsController**。</span><span class="sxs-lookup"><span data-stu-id="608f0-139">To ensure the views are re-created, delete the **Students** folder under **Views**, and delete the **StudentsController**.</span></span> <span data-ttu-id="608f0-140">然后，右键单击**控制器**文件夹，并添加基架**学生**模型。</span><span class="sxs-lookup"><span data-stu-id="608f0-140">Then, right-click the **Controllers** folder and add scaffolding for the **Student** model.</span></span> <span data-ttu-id="608f0-141">同样，将控制器命名**StudentsController**。</span><span class="sxs-lookup"><span data-stu-id="608f0-141">Again, name the controller **StudentsController**.</span></span> <span data-ttu-id="608f0-142">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="608f0-142">Select **Add**.</span></span>

<span data-ttu-id="608f0-143">重新生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="608f0-143">Build the solution again.</span></span> <span data-ttu-id="608f0-144">视图现在包含 MiddleName 属性。</span><span class="sxs-lookup"><span data-stu-id="608f0-144">The views now contain the MiddleName property.</span></span>

![显示中间名](changing-the-database/_static/image5.png)

## <a name="next-steps"></a><span data-ttu-id="608f0-146">后续步骤</span><span class="sxs-lookup"><span data-stu-id="608f0-146">Next steps</span></span>

<span data-ttu-id="608f0-147">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="608f0-147">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="608f0-148">添加列</span><span class="sxs-lookup"><span data-stu-id="608f0-148">Added a column</span></span>
> * <span data-ttu-id="608f0-149">将属性添加到视图的视图</span><span class="sxs-lookup"><span data-stu-id="608f0-149">Added the property to the views</span></span>

<span data-ttu-id="608f0-150">转到下一步的教程，了解如何自定义用于显示有关学生记录的详细信息视图。</span><span class="sxs-lookup"><span data-stu-id="608f0-150">Advance to the next tutorial to learn how to customize the view for showing details about a student record.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="608f0-151">自定义视图</span><span class="sxs-lookup"><span data-stu-id="608f0-151">Customize a view</span></span>](customizing-a-view.md)