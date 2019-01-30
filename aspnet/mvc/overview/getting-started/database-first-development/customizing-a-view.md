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
# <a name="tutorial-customize-view-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="fcd06-103">教程：自定义视图的 EF 数据库第一个与 ASP.NET MVC 应用</span><span class="sxs-lookup"><span data-stu-id="fcd06-103">Tutorial: Customize view for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="fcd06-104">使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="fcd06-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="fcd06-105">本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="fcd06-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="fcd06-106">生成的代码对应于数据库表中的列。</span><span class="sxs-lookup"><span data-stu-id="fcd06-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="fcd06-107">本教程重点介绍更改的自动生成的视图，以增强此演示文稿。</span><span class="sxs-lookup"><span data-stu-id="fcd06-107">This tutorial focuses on changing the automatically-generated views to enhance the presentation.</span></span>

<span data-ttu-id="fcd06-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="fcd06-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fcd06-109">将课程添加到学生详细信息页</span><span class="sxs-lookup"><span data-stu-id="fcd06-109">Add courses to the student detail page</span></span>
> * <span data-ttu-id="fcd06-110">确认课程都添加到页面</span><span class="sxs-lookup"><span data-stu-id="fcd06-110">Confirm that the courses are added to the page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fcd06-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="fcd06-111">Prerequisites</span></span>

* [<span data-ttu-id="fcd06-112">更改数据库</span><span class="sxs-lookup"><span data-stu-id="fcd06-112">Change the database</span></span>](changing-the-database.md)

## <a name="add-courses-to-student-detail"></a><span data-ttu-id="fcd06-113">将课程添加到学生详细信息</span><span class="sxs-lookup"><span data-stu-id="fcd06-113">Add courses to student detail</span></span>

<span data-ttu-id="fcd06-114">生成的代码为应用程序提供很好的起点，但它不一定提供全部所需应用程序中的功能。</span><span class="sxs-lookup"><span data-stu-id="fcd06-114">The generated code provides a good starting point for your application but it does not necessarily provide all of the functionality that you need in your application.</span></span> <span data-ttu-id="fcd06-115">可以自定义代码来满足你的应用程序的特定要求。</span><span class="sxs-lookup"><span data-stu-id="fcd06-115">You can customize the code to meet the particular requirements of your application.</span></span> <span data-ttu-id="fcd06-116">目前，你的应用程序不显示所选学生的已注册的课程。</span><span class="sxs-lookup"><span data-stu-id="fcd06-116">Currently, your application does not display the enrolled courses for the selected student.</span></span> <span data-ttu-id="fcd06-117">在本部分中，您将添加已注册的课程到每个学生**详细信息**学生的视图。</span><span class="sxs-lookup"><span data-stu-id="fcd06-117">In this section, you will add the enrolled courses for each student to the **Details** view for the student.</span></span>

<span data-ttu-id="fcd06-118">打开**视图** > **学生** > *Details.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="fcd06-118">Open **Views** > **Students** > *Details.cshtml*.</span></span> <span data-ttu-id="fcd06-119">最后一个下面&lt;/dl&gt;标记，但在关闭前&lt;/div&gt;标记中，添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="fcd06-119">Below the last &lt;/dl&gt; tag, but before the closing &lt;/div&gt; tag, add the following code.</span></span>

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

<span data-ttu-id="fcd06-120">此代码将创建所选学生 Enrollment 表中显示为每个记录的行的表。</span><span class="sxs-lookup"><span data-stu-id="fcd06-120">This code creates a table that displays a row for each record in the Enrollment table for the selected student.</span></span> <span data-ttu-id="fcd06-121">**显示**方法表示表达式的对象 (modelItem) 将呈现 HTML。</span><span class="sxs-lookup"><span data-stu-id="fcd06-121">The **Display** method renders HTML for the object (modelItem) that represents the expression.</span></span> <span data-ttu-id="fcd06-122">使用 Display 方法 （而不是只需在代码中嵌入的属性值） 以确保设置的值格式正确基于其类型和该类型的模板。</span><span class="sxs-lookup"><span data-stu-id="fcd06-122">You use the Display method (rather than simply embedding the property value in the code) to make sure the value is formatted correctly based on its type and the template for that type.</span></span> <span data-ttu-id="fcd06-123">在此示例中，从当前记录在循环中，每个表达式返回的单个属性和值是基元类型的呈现为文本。</span><span class="sxs-lookup"><span data-stu-id="fcd06-123">In this example, each expression returns a single property from the current record in the loop, and the values are primitive types which are rendered as text.</span></span>

## <a name="confirm-courses-are-added"></a><span data-ttu-id="fcd06-124">确认也会添加课程</span><span class="sxs-lookup"><span data-stu-id="fcd06-124">Confirm courses are added</span></span>

<span data-ttu-id="fcd06-125">运行该解决方案。</span><span class="sxs-lookup"><span data-stu-id="fcd06-125">Run the solution.</span></span> <span data-ttu-id="fcd06-126">单击**学生列表**，然后选择**详细信息**一个学生。</span><span class="sxs-lookup"><span data-stu-id="fcd06-126">Click **List of students** and select **Details** for one of the students.</span></span> <span data-ttu-id="fcd06-127">你将看到在视图中包含了已注册的课程。</span><span class="sxs-lookup"><span data-stu-id="fcd06-127">You will see the enrolled courses have been included in the view.</span></span>

![与注册的学生](customizing-a-view/_static/image1.png)

## <a name="next-steps"></a><span data-ttu-id="fcd06-129">后续步骤</span><span class="sxs-lookup"><span data-stu-id="fcd06-129">Next steps</span></span>
<span data-ttu-id="fcd06-130">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="fcd06-130">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fcd06-131">添加了的课程为学生详细信息页</span><span class="sxs-lookup"><span data-stu-id="fcd06-131">Added courses to the student detail page</span></span>
> * <span data-ttu-id="fcd06-132">确认课程都添加到页面</span><span class="sxs-lookup"><span data-stu-id="fcd06-132">Confirmed that the courses are added to the page</span></span>

<span data-ttu-id="fcd06-133">转到下一步的教程，了解如何添加数据批注以指定验证要求和显示格式。</span><span class="sxs-lookup"><span data-stu-id="fcd06-133">Advance to the next tutorial to learn how to add data annotations to specify validation requirements and display formatting.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="fcd06-134">增强数据验证</span><span class="sxs-lookup"><span data-stu-id="fcd06-134">Enhance data validation</span></span>](enhancing-data-validation.md)
