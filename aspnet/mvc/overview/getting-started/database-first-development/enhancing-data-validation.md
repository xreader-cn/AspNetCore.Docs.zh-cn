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
ms.openlocfilehash: 897cd7c6a40445e2a4abede50d81e101372d3233
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667617"
---
# <a name="tutorial-enhance-data-validation-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="9046f-103">教程：增强 EF Database First 使用 ASP.NET MVC 应用程序的数据的验证</span><span class="sxs-lookup"><span data-stu-id="9046f-103">Tutorial: Enhance data validation for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="9046f-104">使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9046f-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="9046f-105">本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="9046f-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="9046f-106">生成的代码对应于数据库表中的列。</span><span class="sxs-lookup"><span data-stu-id="9046f-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="9046f-107">本教程重点介绍将数据批注添加到数据模型以指定验证要求和显示格式。</span><span class="sxs-lookup"><span data-stu-id="9046f-107">This tutorial focuses on adding data annotations to the data model to specify validation requirements and display formatting.</span></span> <span data-ttu-id="9046f-108">它改进了基于注释部分中的用户的反馈。</span><span class="sxs-lookup"><span data-stu-id="9046f-108">It was improved based on feedback from users in the comments section.</span></span>

<span data-ttu-id="9046f-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="9046f-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9046f-110">添加数据批注</span><span class="sxs-lookup"><span data-stu-id="9046f-110">Add data annotations</span></span>
> * <span data-ttu-id="9046f-111">添加元数据类</span><span class="sxs-lookup"><span data-stu-id="9046f-111">Add metadata classes</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9046f-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="9046f-112">Prerequisites</span></span>

* [<span data-ttu-id="9046f-113">自定义视图</span><span class="sxs-lookup"><span data-stu-id="9046f-113">Customize a view</span></span>](customizing-a-view.md)

## <a name="add-data-annotations"></a><span data-ttu-id="9046f-114">添加数据批注</span><span class="sxs-lookup"><span data-stu-id="9046f-114">Add data annotations</span></span>

<span data-ttu-id="9046f-115">正如您看到在之前主题中，一些数据的验证规则会自动应用到用户输入。</span><span class="sxs-lookup"><span data-stu-id="9046f-115">As you saw in an earlier topic, some data validation rules are automatically applied to the user input.</span></span> <span data-ttu-id="9046f-116">例如，可以仅级属性提供一个数字。</span><span class="sxs-lookup"><span data-stu-id="9046f-116">For example, you can only provide a number for the Grade property.</span></span> <span data-ttu-id="9046f-117">若要指定多个数据验证规则，可以向 model 类中添加数据批注。</span><span class="sxs-lookup"><span data-stu-id="9046f-117">To specify more data validation rules, you can add data annotations to your model class.</span></span> <span data-ttu-id="9046f-118">这些批注将应用于整个 web 应用程序为指定的属性。</span><span class="sxs-lookup"><span data-stu-id="9046f-118">These annotations are applied throughout your web application for the specified property.</span></span> <span data-ttu-id="9046f-119">您还可以应用更改属性的显示方式; 的格式设置属性例如，更改使用的文本标签的值。</span><span class="sxs-lookup"><span data-stu-id="9046f-119">You can also apply formatting attributes that change how the properties are displayed; such as, changing the value used for text labels.</span></span>

<span data-ttu-id="9046f-120">在本教程中，您将添加数据注释来限制为 FirstName、 LastName 和 MiddleName 属性提供的值的长度。</span><span class="sxs-lookup"><span data-stu-id="9046f-120">In this tutorial, you will add data annotations to restrict the length of the values provided for the FirstName, LastName, and MiddleName properties.</span></span> <span data-ttu-id="9046f-121">在数据库中，这些值是 50 个字符;但是，在 web 应用程序中的字符数限制为当前不强制执行。</span><span class="sxs-lookup"><span data-stu-id="9046f-121">In the database, these values are limited to 50 characters; however, in your web application that character limit is currently not enforced.</span></span> <span data-ttu-id="9046f-122">如果用户提供超过 50 个字符，这些值之一，尝试将值保存到数据库时将崩溃页。</span><span class="sxs-lookup"><span data-stu-id="9046f-122">If a user provides more than 50 characters for one of those values, the page will crash when attempting to save the value to the database.</span></span> <span data-ttu-id="9046f-123">你还将为介于 0 和 4 之间的值限制级。</span><span class="sxs-lookup"><span data-stu-id="9046f-123">You will also restrict Grade to values between 0 and 4.</span></span>

<span data-ttu-id="9046f-124">选择**模型** > **ContosoModel.edmx** > **ContosoModel.tt** ，然后打开*Student.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="9046f-124">Select **Models** > **ContosoModel.edmx** > **ContosoModel.tt** and open the *Student.cs* file.</span></span> <span data-ttu-id="9046f-125">向类添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="9046f-125">Add the following highlighted code to the class.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

<span data-ttu-id="9046f-126">打开*Enrollment.cs*并添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="9046f-126">Open *Enrollment.cs* and add the following highlighted code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

<span data-ttu-id="9046f-127">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="9046f-127">Build the solution.</span></span>

<span data-ttu-id="9046f-128">单击**学生列表**，然后选择**编辑**。</span><span class="sxs-lookup"><span data-stu-id="9046f-128">Click **List of students** and select **Edit**.</span></span> <span data-ttu-id="9046f-129">如果尝试输入超过 50 个字符，显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="9046f-129">If you attempt to enter more than 50 characters, an error message is displayed.</span></span>

![显示错误消息](enhancing-data-validation/_static/image1.png)

<span data-ttu-id="9046f-131">返回到主页页面。</span><span class="sxs-lookup"><span data-stu-id="9046f-131">Go back to the home page.</span></span> <span data-ttu-id="9046f-132">单击**注册列表**，然后选择**编辑**。</span><span class="sxs-lookup"><span data-stu-id="9046f-132">Click **List of enrollments** and select **Edit**.</span></span> <span data-ttu-id="9046f-133">尝试提供 4 级。</span><span class="sxs-lookup"><span data-stu-id="9046f-133">Attempt to provide a grade above 4.</span></span> <span data-ttu-id="9046f-134">您将收到此错误：*该字段级必须介于 0 和 4 之间。*</span><span class="sxs-lookup"><span data-stu-id="9046f-134">You will receive this error: *The field Grade must be between 0 and 4.*</span></span>

## <a name="add-metadata-classes"></a><span data-ttu-id="9046f-135">添加元数据类</span><span class="sxs-lookup"><span data-stu-id="9046f-135">Add metadata classes</span></span>

<span data-ttu-id="9046f-136">当不希望更改; 的数据库时，直接向 model 类添加验证特性有效但是，如果更改了数据库，并且需要重新生成模型类，您将丢失所有已应用于模型类的属性。</span><span class="sxs-lookup"><span data-stu-id="9046f-136">Adding the validation attributes directly to the model class works when you do not expect the database to change; however, if your database changes and you need to regenerate the model class, you will lose all of the attributes you had applied to the model class.</span></span> <span data-ttu-id="9046f-137">这种方法可以是非常低效，而且容易丢失重要的验证规则。</span><span class="sxs-lookup"><span data-stu-id="9046f-137">This approach can be very inefficient and prone to losing important validation rules.</span></span>

<span data-ttu-id="9046f-138">若要避免此问题，可以添加一个包含属性的元数据类。</span><span class="sxs-lookup"><span data-stu-id="9046f-138">To avoid this problem, you can add a metadata class that contains the attributes.</span></span> <span data-ttu-id="9046f-139">当将关联到元数据类的模型类时，这些属性应用于该模型。</span><span class="sxs-lookup"><span data-stu-id="9046f-139">When you associate the model class to the metadata class, those attributes are applied to the model.</span></span> <span data-ttu-id="9046f-140">在此方法中，模型类可以重新生成而不会丢失所有已应用于元数据类的属性。</span><span class="sxs-lookup"><span data-stu-id="9046f-140">In this approach, the model class can be regenerated without losing all of the attributes that have been applied to the metadata class.</span></span>

<span data-ttu-id="9046f-141">在中**模型**文件夹中，添加一个名为类*Metadata.cs*。</span><span class="sxs-lookup"><span data-stu-id="9046f-141">In the **Models** folder, add a class named *Metadata.cs*.</span></span>

<span data-ttu-id="9046f-142">中的代码替换*Metadata.cs*用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="9046f-142">Replace the code in *Metadata.cs* with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

<span data-ttu-id="9046f-143">这些元数据类包含所有先前应用到模型类的验证特性。</span><span class="sxs-lookup"><span data-stu-id="9046f-143">These metadata classes contain all of the validation attributes that you had previously applied to the model classes.</span></span> <span data-ttu-id="9046f-144">**显示**特性用于更改使用的文本标签的值。</span><span class="sxs-lookup"><span data-stu-id="9046f-144">The **Display** attribute is used to change the value used for text labels.</span></span>

<span data-ttu-id="9046f-145">现在，必须将模型类关联的元数据类。</span><span class="sxs-lookup"><span data-stu-id="9046f-145">Now, you must associate the model classes with the metadata classes.</span></span>

<span data-ttu-id="9046f-146">在中**模型**文件夹中，添加一个名为类*PartialClasses.cs*。</span><span class="sxs-lookup"><span data-stu-id="9046f-146">In the **Models** folder, add a class named *PartialClasses.cs*.</span></span>

<span data-ttu-id="9046f-147">将以下代码替换为该文件的内容。</span><span class="sxs-lookup"><span data-stu-id="9046f-147">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

<span data-ttu-id="9046f-148">请注意，每个类标记为`partial`类，并且每个匹配的名称和命名空间为自动生成的类。</span><span class="sxs-lookup"><span data-stu-id="9046f-148">Notice that each class is marked as a `partial` class, and each matches the name and namespace as the class that is automatically generated.</span></span> <span data-ttu-id="9046f-149">通过将元数据特性应用到分部类，可确保数据验证属性，将应用到自动生成类。</span><span class="sxs-lookup"><span data-stu-id="9046f-149">By applying the metadata attribute to the partial class, you ensure that the data validation attributes will be applied to the automatically-generated class.</span></span> <span data-ttu-id="9046f-150">当你重新生成模型类，因为在不重新生成的分部类中应用的元数据特性，这些属性不会丢失。</span><span class="sxs-lookup"><span data-stu-id="9046f-150">These attributes will not be lost when you regenerate the model classes because the metadata attribute is applied in partial classes that are not regenerated.</span></span>

<span data-ttu-id="9046f-151">若要重新生成会自动生成的类，请打开*ContosoModel.edmx*文件。</span><span class="sxs-lookup"><span data-stu-id="9046f-151">To regenerate the automatically-generated classes, open the *ContosoModel.edmx* file.</span></span> <span data-ttu-id="9046f-152">再次重申，右键单击设计图面，然后选择**从数据库更新模型**。</span><span class="sxs-lookup"><span data-stu-id="9046f-152">Once again, right-click on the design surface and select **Update Model from Database**.</span></span> <span data-ttu-id="9046f-153">即使没有更改数据库，此过程将重新生成类。</span><span class="sxs-lookup"><span data-stu-id="9046f-153">Even though you have not changed the database, this process will regenerate the classes.</span></span> <span data-ttu-id="9046f-154">在中**刷新**选项卡上，选择**表**并**完成**。</span><span class="sxs-lookup"><span data-stu-id="9046f-154">In the **Refresh** tab, select **Tables** and **Finish**.</span></span>

<span data-ttu-id="9046f-155">保存*ContosoModel.edmx*文件以应用所做的更改。</span><span class="sxs-lookup"><span data-stu-id="9046f-155">Save the *ContosoModel.edmx* file to apply the changes.</span></span>

<span data-ttu-id="9046f-156">打开*Student.cs*文件或*Enrollment.cs*文件，并请注意，您先前应用的数据验证属性将不再在文件中。</span><span class="sxs-lookup"><span data-stu-id="9046f-156">Open the *Student.cs* file or the *Enrollment.cs* file, and notice that the data validation attributes you applied earlier are no longer in the file.</span></span> <span data-ttu-id="9046f-157">但是，运行该应用程序，并请注意，当输入数据时仍应用验证规则。</span><span class="sxs-lookup"><span data-stu-id="9046f-157">However, run the application, and notice that the validation rules are still applied when you enter data.</span></span>

## <a name="conclusion"></a><span data-ttu-id="9046f-158">结束语</span><span class="sxs-lookup"><span data-stu-id="9046f-158">Conclusion</span></span>

<span data-ttu-id="9046f-159">本系列提供一个简单的示例说明如何从现有数据库，使用户能够编辑、 更新、 创建和删除数据生成代码。</span><span class="sxs-lookup"><span data-stu-id="9046f-159">This series provided a simple example of how to generate code from an existing database that enables users to edit, update, create and delete data.</span></span> <span data-ttu-id="9046f-160">它使用 ASP.NET MVC 5，实体框架和 ASP.NET 基架创建项目。</span><span class="sxs-lookup"><span data-stu-id="9046f-160">It used ASP.NET MVC 5, Entity Framework and ASP.NET Scaffolding to create the project.</span></span> 

<span data-ttu-id="9046f-161">Code First 开发的介绍性示例，请参阅[Getting Started with ASP.NET MVC 5](../introduction/getting-started.md)。</span><span class="sxs-lookup"><span data-stu-id="9046f-161">For an introductory example of Code First development, see [Getting Started with ASP.NET MVC 5](../introduction/getting-started.md).</span></span> 

<span data-ttu-id="9046f-162">有关更高级的示例，请参阅[为 ASP.NET MVC 4 应用程序创建实体框架数据模型](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)。</span><span class="sxs-lookup"><span data-stu-id="9046f-162">For a more advanced example, see [Creating an Entity Framework Data Model for an ASP.NET MVC 4 App](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span> <span data-ttu-id="9046f-163">请注意，使用第一个数据库中的数据使用 DbContext API 是用于在第一个代码中使用数据的 API 相同。</span><span class="sxs-lookup"><span data-stu-id="9046f-163">Note that the DbContext API that you use for working with data in Database First is the same as the API you use for working with data in Code First.</span></span> <span data-ttu-id="9046f-164">即使你想要使用第一个数据库，您可以了解如何处理更复杂的方案，如读取和更新相关的数据，处理并发冲突，请从代码第一个教程，等等。</span><span class="sxs-lookup"><span data-stu-id="9046f-164">Even if you intend to use Database First, you can learn how to handle more complex scenarios such as reading and updating related data, handling concurrency conflicts, and so forth from a Code First tutorial.</span></span> <span data-ttu-id="9046f-165">如何创建数据库、 上下文类和实体类中是唯一的区别。</span><span class="sxs-lookup"><span data-stu-id="9046f-165">The only difference is in how the database, context class, and entity classes are created.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9046f-166">其他资源</span><span class="sxs-lookup"><span data-stu-id="9046f-166">Additional resources</span></span>

<span data-ttu-id="9046f-167">可以将应用于属性和类的数据验证注释的完整列表，请参阅[System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9046f-167">For a full list of data validation annotations you can apply to properties and classes, see [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span></span>

## <a name="next-steps"></a><span data-ttu-id="9046f-168">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9046f-168">Next steps</span></span>

<span data-ttu-id="9046f-169">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="9046f-169">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9046f-170">添加的数据批注</span><span class="sxs-lookup"><span data-stu-id="9046f-170">Added data annotations</span></span>
> * <span data-ttu-id="9046f-171">添加元数据类</span><span class="sxs-lookup"><span data-stu-id="9046f-171">Added metadata classes</span></span>

<span data-ttu-id="9046f-172">若要了解如何将 web 应用和 SQL 数据库部署到 Azure 应用服务，请参阅以下教程：</span><span class="sxs-lookup"><span data-stu-id="9046f-172">To learn how to deploy a web app and SQL database to Azure App Service, see this tutorial:</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="9046f-173">.NET 应用部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="9046f-173">Deploy a .NET app to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase/)
