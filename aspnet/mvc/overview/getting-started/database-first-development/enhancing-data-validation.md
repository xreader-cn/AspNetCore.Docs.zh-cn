---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 教程：增强 EF Database First 使用 ASP.NET MVC 应用程序的数据的验证
description: 本教程重点介绍将数据批注添加到数据模型以指定验证要求和显示格式。
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: 85299d70c6cba52c1d40a42edfd429c96318134a
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236479"
---
# <a name="tutorial-enhance-data-validation-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="1347e-103">教程：增强 EF Database First 使用 ASP.NET MVC 应用程序的数据的验证</span><span class="sxs-lookup"><span data-stu-id="1347e-103">Tutorial: Enhance data validation for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="1347e-104">使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="1347e-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="1347e-105">本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="1347e-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="1347e-106">生成的代码对应于数据库表中的列。</span><span class="sxs-lookup"><span data-stu-id="1347e-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="1347e-107">本教程重点介绍将数据批注添加到数据模型以指定验证要求和显示格式。</span><span class="sxs-lookup"><span data-stu-id="1347e-107">This tutorial focuses on adding data annotations to the data model to specify validation requirements and display formatting.</span></span> <span data-ttu-id="1347e-108">它改进了基于注释部分中的用户的反馈。</span><span class="sxs-lookup"><span data-stu-id="1347e-108">It was improved based on feedback from users in the comments section.</span></span>

<span data-ttu-id="1347e-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1347e-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1347e-110">添加数据批注</span><span class="sxs-lookup"><span data-stu-id="1347e-110">Add data annotations</span></span>
> * <span data-ttu-id="1347e-111">添加元数据类</span><span class="sxs-lookup"><span data-stu-id="1347e-111">Add metadata classes</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1347e-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="1347e-112">Prerequisites</span></span>

* [<span data-ttu-id="1347e-113">自定义视图</span><span class="sxs-lookup"><span data-stu-id="1347e-113">Customize a view</span></span>](customizing-a-view.md)

## <a name="add-data-annotations"></a><span data-ttu-id="1347e-114">添加数据批注</span><span class="sxs-lookup"><span data-stu-id="1347e-114">Add data annotations</span></span>

<span data-ttu-id="1347e-115">正如您看到在之前主题中，一些数据的验证规则会自动应用到用户输入。</span><span class="sxs-lookup"><span data-stu-id="1347e-115">As you saw in an earlier topic, some data validation rules are automatically applied to the user input.</span></span> <span data-ttu-id="1347e-116">例如，可以仅级属性提供一个数字。</span><span class="sxs-lookup"><span data-stu-id="1347e-116">For example, you can only provide a number for the Grade property.</span></span> <span data-ttu-id="1347e-117">若要指定多个数据验证规则，可以向 model 类中添加数据批注。</span><span class="sxs-lookup"><span data-stu-id="1347e-117">To specify more data validation rules, you can add data annotations to your model class.</span></span> <span data-ttu-id="1347e-118">这些批注将应用于整个 web 应用程序为指定的属性。</span><span class="sxs-lookup"><span data-stu-id="1347e-118">These annotations are applied throughout your web application for the specified property.</span></span> <span data-ttu-id="1347e-119">您还可以应用更改属性的显示方式; 的格式设置属性例如，更改使用的文本标签的值。</span><span class="sxs-lookup"><span data-stu-id="1347e-119">You can also apply formatting attributes that change how the properties are displayed; such as, changing the value used for text labels.</span></span>

<span data-ttu-id="1347e-120">在本教程中，您将添加数据注释来限制为 FirstName、 LastName 和 MiddleName 属性提供的值的长度。</span><span class="sxs-lookup"><span data-stu-id="1347e-120">In this tutorial, you will add data annotations to restrict the length of the values provided for the FirstName, LastName, and MiddleName properties.</span></span> <span data-ttu-id="1347e-121">在数据库中，这些值是 50 个字符;但是，在 web 应用程序中的字符数限制为当前不强制执行。</span><span class="sxs-lookup"><span data-stu-id="1347e-121">In the database, these values are limited to 50 characters; however, in your web application that character limit is currently not enforced.</span></span> <span data-ttu-id="1347e-122">如果用户提供超过 50 个字符，这些值之一，尝试将值保存到数据库时将崩溃页。</span><span class="sxs-lookup"><span data-stu-id="1347e-122">If a user provides more than 50 characters for one of those values, the page will crash when attempting to save the value to the database.</span></span> <span data-ttu-id="1347e-123">你还将为介于 0 和 4 之间的值限制级。</span><span class="sxs-lookup"><span data-stu-id="1347e-123">You will also restrict Grade to values between 0 and 4.</span></span>

<span data-ttu-id="1347e-124">选择**模型** > **ContosoModel.edmx** > **ContosoModel.tt** ，然后打开*Student.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="1347e-124">Select **Models** > **ContosoModel.edmx** > **ContosoModel.tt** and open the *Student.cs* file.</span></span> <span data-ttu-id="1347e-125">向类添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="1347e-125">Add the following highlighted code to the class.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

<span data-ttu-id="1347e-126">打开*Enrollment.cs*并添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="1347e-126">Open *Enrollment.cs* and add the following highlighted code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

<span data-ttu-id="1347e-127">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="1347e-127">Build the solution.</span></span>

<span data-ttu-id="1347e-128">单击**学生列表**，然后选择**编辑**。</span><span class="sxs-lookup"><span data-stu-id="1347e-128">Click **List of students** and select **Edit**.</span></span> <span data-ttu-id="1347e-129">如果尝试输入超过 50 个字符，显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="1347e-129">If you attempt to enter more than 50 characters, an error message is displayed.</span></span>

![显示错误消息](enhancing-data-validation/_static/image1.png)

<span data-ttu-id="1347e-131">返回到主页页面。</span><span class="sxs-lookup"><span data-stu-id="1347e-131">Go back to the home page.</span></span> <span data-ttu-id="1347e-132">单击**注册列表**，然后选择**编辑**。</span><span class="sxs-lookup"><span data-stu-id="1347e-132">Click **List of enrollments** and select **Edit**.</span></span> <span data-ttu-id="1347e-133">尝试提供 4 级。</span><span class="sxs-lookup"><span data-stu-id="1347e-133">Attempt to provide a grade above 4.</span></span> <span data-ttu-id="1347e-134">您将收到此错误：*该字段级必须介于 0 和 4 之间。*</span><span class="sxs-lookup"><span data-stu-id="1347e-134">You will receive this error: *The field Grade must be between 0 and 4.*</span></span>

## <a name="add-metadata-classes"></a><span data-ttu-id="1347e-135">添加元数据类</span><span class="sxs-lookup"><span data-stu-id="1347e-135">Add metadata classes</span></span>

<span data-ttu-id="1347e-136">当不希望更改; 的数据库时，直接向 model 类添加验证特性有效但是，如果更改了数据库，并且需要重新生成模型类，您将丢失所有已应用于模型类的属性。</span><span class="sxs-lookup"><span data-stu-id="1347e-136">Adding the validation attributes directly to the model class works when you do not expect the database to change; however, if your database changes and you need to regenerate the model class, you will lose all of the attributes you had applied to the model class.</span></span> <span data-ttu-id="1347e-137">这种方法可以是非常低效，而且容易丢失重要的验证规则。</span><span class="sxs-lookup"><span data-stu-id="1347e-137">This approach can be very inefficient and prone to losing important validation rules.</span></span>

<span data-ttu-id="1347e-138">若要避免此问题，可以添加一个包含属性的元数据类。</span><span class="sxs-lookup"><span data-stu-id="1347e-138">To avoid this problem, you can add a metadata class that contains the attributes.</span></span> <span data-ttu-id="1347e-139">当将关联到元数据类的模型类时，这些属性应用于该模型。</span><span class="sxs-lookup"><span data-stu-id="1347e-139">When you associate the model class to the metadata class, those attributes are applied to the model.</span></span> <span data-ttu-id="1347e-140">在此方法中，模型类可以重新生成而不会丢失所有已应用于元数据类的属性。</span><span class="sxs-lookup"><span data-stu-id="1347e-140">In this approach, the model class can be regenerated without losing all of the attributes that have been applied to the metadata class.</span></span>

<span data-ttu-id="1347e-141">在中**模型**文件夹中，添加一个名为类*Metadata.cs*。</span><span class="sxs-lookup"><span data-stu-id="1347e-141">In the **Models** folder, add a class named *Metadata.cs*.</span></span>

<span data-ttu-id="1347e-142">中的代码替换*Metadata.cs*用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="1347e-142">Replace the code in *Metadata.cs* with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

<span data-ttu-id="1347e-143">这些元数据类包含所有先前应用到模型类的验证特性。</span><span class="sxs-lookup"><span data-stu-id="1347e-143">These metadata classes contain all of the validation attributes that you had previously applied to the model classes.</span></span> <span data-ttu-id="1347e-144">**显示**特性用于更改使用的文本标签的值。</span><span class="sxs-lookup"><span data-stu-id="1347e-144">The **Display** attribute is used to change the value used for text labels.</span></span>

<span data-ttu-id="1347e-145">现在，必须将模型类关联的元数据类。</span><span class="sxs-lookup"><span data-stu-id="1347e-145">Now, you must associate the model classes with the metadata classes.</span></span>

<span data-ttu-id="1347e-146">在中**模型**文件夹中，添加一个名为类*PartialClasses.cs*。</span><span class="sxs-lookup"><span data-stu-id="1347e-146">In the **Models** folder, add a class named *PartialClasses.cs*.</span></span>

<span data-ttu-id="1347e-147">将以下代码替换为该文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1347e-147">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

<span data-ttu-id="1347e-148">请注意，每个类标记为`partial`类，并且每个匹配的名称和命名空间为自动生成的类。</span><span class="sxs-lookup"><span data-stu-id="1347e-148">Notice that each class is marked as a `partial` class, and each matches the name and namespace as the class that is automatically generated.</span></span> <span data-ttu-id="1347e-149">通过将元数据特性应用到分部类，可确保数据验证属性，将应用到自动生成类。</span><span class="sxs-lookup"><span data-stu-id="1347e-149">By applying the metadata attribute to the partial class, you ensure that the data validation attributes will be applied to the automatically-generated class.</span></span> <span data-ttu-id="1347e-150">当你重新生成模型类，因为在不重新生成的分部类中应用的元数据特性，这些属性不会丢失。</span><span class="sxs-lookup"><span data-stu-id="1347e-150">These attributes will not be lost when you regenerate the model classes because the metadata attribute is applied in partial classes that are not regenerated.</span></span>

<span data-ttu-id="1347e-151">若要重新生成会自动生成的类，请打开*ContosoModel.edmx*文件。</span><span class="sxs-lookup"><span data-stu-id="1347e-151">To regenerate the automatically-generated classes, open the *ContosoModel.edmx* file.</span></span> <span data-ttu-id="1347e-152">再次重申，右键单击设计图面，然后选择**从数据库更新模型**。</span><span class="sxs-lookup"><span data-stu-id="1347e-152">Once again, right-click on the design surface and select **Update Model from Database**.</span></span> <span data-ttu-id="1347e-153">即使没有更改数据库，此过程将重新生成类。</span><span class="sxs-lookup"><span data-stu-id="1347e-153">Even though you have not changed the database, this process will regenerate the classes.</span></span> <span data-ttu-id="1347e-154">在中**刷新**选项卡上，选择**表**并**完成**。</span><span class="sxs-lookup"><span data-stu-id="1347e-154">In the **Refresh** tab, select **Tables** and **Finish**.</span></span>

<span data-ttu-id="1347e-155">保存*ContosoModel.edmx*文件以应用所做的更改。</span><span class="sxs-lookup"><span data-stu-id="1347e-155">Save the *ContosoModel.edmx* file to apply the changes.</span></span>

<span data-ttu-id="1347e-156">打开*Student.cs*文件或*Enrollment.cs*文件，并请注意，您先前应用的数据验证属性将不再在文件中。</span><span class="sxs-lookup"><span data-stu-id="1347e-156">Open the *Student.cs* file or the *Enrollment.cs* file, and notice that the data validation attributes you applied earlier are no longer in the file.</span></span> <span data-ttu-id="1347e-157">但是，运行该应用程序，并请注意，当输入数据时仍应用验证规则。</span><span class="sxs-lookup"><span data-stu-id="1347e-157">However, run the application, and notice that the validation rules are still applied when you enter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1347e-158">其他资源</span><span class="sxs-lookup"><span data-stu-id="1347e-158">Additional resources</span></span>

<span data-ttu-id="1347e-159">可以将应用于属性和类的数据验证注释的完整列表，请参阅[System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx)。</span><span class="sxs-lookup"><span data-stu-id="1347e-159">For a full list of data validation annotations you can apply to properties and classes, see [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span></span>

## <a name="next-steps"></a><span data-ttu-id="1347e-160">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1347e-160">Next steps</span></span>

<span data-ttu-id="1347e-161">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1347e-161">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1347e-162">添加的数据批注</span><span class="sxs-lookup"><span data-stu-id="1347e-162">Added data annotations</span></span>
> * <span data-ttu-id="1347e-163">添加元数据类</span><span class="sxs-lookup"><span data-stu-id="1347e-163">Added metadata classes</span></span>

<span data-ttu-id="1347e-164">转到下一步的教程，了解如何将 web 应用和数据库发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1347e-164">Advance to the next tutorial to learn how to publish the web app and database to Azure.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="1347e-165">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="1347e-165">Publish to Azure</span></span>](publish-to-azure.md)