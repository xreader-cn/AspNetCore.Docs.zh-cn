---
uid: web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
title: 检索和显示数据与模型绑定和 web 窗体 |Microsoft Docs
author: Rick-Anderson
description: 本系列教程演示了一个 ASP.NET Web 窗体项目中使用模型绑定的基本方面。 模型绑定使数据交互...更多直接-
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 9f24fb82-c7ac-48da-b8e2-51b3da17e365
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
msc.type: authoredcontent
ms.openlocfilehash: c53c27f4852eab9813bd917315111e7cd3b04953
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396280"
---
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a><span data-ttu-id="20f87-104">检索和使用模型绑定和 web 窗体显示数据</span><span class="sxs-lookup"><span data-stu-id="20f87-104">Retrieving and displaying data with model binding and web forms</span></span>
====================

> <span data-ttu-id="20f87-105">本系列教程演示了一个 ASP.NET Web 窗体项目中使用模型绑定的基本方面。</span><span class="sxs-lookup"><span data-stu-id="20f87-105">This tutorial series demonstrates basic aspects of using model binding with an ASP.NET Web Forms project.</span></span> <span data-ttu-id="20f87-106">模型绑定可以更直接的比处理数据源对象 （如 ObjectDataSource 或 SqlDataSource） 的数据交互。</span><span class="sxs-lookup"><span data-stu-id="20f87-106">Model binding makes data interaction more straight-forward than dealing with data source objects (such as ObjectDataSource or SqlDataSource).</span></span> <span data-ttu-id="20f87-107">本系列开始介绍性材料，后续教程将移动到更高级的概念。</span><span class="sxs-lookup"><span data-stu-id="20f87-107">This series starts with introductory material and moves to more advanced concepts in later tutorials.</span></span>
> 
>  <span data-ttu-id="20f87-108">模型绑定模式适用于任何数据访问技术。</span><span class="sxs-lookup"><span data-stu-id="20f87-108">The model binding pattern works with any data access technology.</span></span> <span data-ttu-id="20f87-109">在本教程中，将使用实体框架中，但您可以使用最熟悉的数据访问技术。</span><span class="sxs-lookup"><span data-stu-id="20f87-109">In this tutorial, you will use Entity Framework, but you could use the data access technology that is most familiar to you.</span></span> <span data-ttu-id="20f87-110">从数据绑定服务器控件，如 GridView、 ListView、 detailsview 和 FormView 控件，指定要用于选择、 更新、 删除和创建数据的方法的名称。</span><span class="sxs-lookup"><span data-stu-id="20f87-110">From a data-bound server control, such as a GridView, ListView, DetailsView, or FormView control, you specify the names of the methods to use for selecting, updating, deleting, and creating data.</span></span> <span data-ttu-id="20f87-111">在本教程中，将 SelectMethod 为指定值。</span><span class="sxs-lookup"><span data-stu-id="20f87-111">In this tutorial, you will specify a value for the SelectMethod.</span></span> 
> 
> <span data-ttu-id="20f87-112">在该方法中，您提供用于检索数据的逻辑。</span><span class="sxs-lookup"><span data-stu-id="20f87-112">Within that method, you provide the logic for retrieving the data.</span></span> <span data-ttu-id="20f87-113">在下一步的教程中，将为 UpdateMethod、 DeleteMethod 和 InsertMethod 设置值。</span><span class="sxs-lookup"><span data-stu-id="20f87-113">In the next tutorial, you will set values for UpdateMethod, DeleteMethod and InsertMethod.</span></span>
>
> <span data-ttu-id="20f87-114">你可以[下载](https://go.microsoft.com/fwlink/?LinkId=286116)中的完整项目C#或 Visual Basic。</span><span class="sxs-lookup"><span data-stu-id="20f87-114">You can [download](https://go.microsoft.com/fwlink/?LinkId=286116) the complete project in C# or Visual Basic.</span></span> <span data-ttu-id="20f87-115">可下载代码适用于 Visual Studio 2012 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="20f87-115">The downloadable code works with Visual Studio 2012 and later.</span></span> <span data-ttu-id="20f87-116">它使用 Visual Studio 2012 模板，为在本教程中所示的 Visual Studio 2017 模板稍有不同。</span><span class="sxs-lookup"><span data-stu-id="20f87-116">It uses the Visual Studio 2012 template, which is slightly different than the Visual Studio 2017 template shown in this tutorial.</span></span>
> 
> <span data-ttu-id="20f87-117">在本教程在 Visual Studio 中运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="20f87-117">In the tutorial you run the application in Visual Studio.</span></span> <span data-ttu-id="20f87-118">此外可以部署到宿主提供程序应用程序，并使其可通过 internet。</span><span class="sxs-lookup"><span data-stu-id="20f87-118">You can also deploy the application to a hosting provider and make it available over the internet.</span></span> <span data-ttu-id="20f87-119">Microsoft 提供了免费的 web 宿主中的最多 10 个网站</span><span class="sxs-lookup"><span data-stu-id="20f87-119">Microsoft offers free web hosting for up to 10 web sites in a</span></span>  
>  <span data-ttu-id="20f87-120">[Azure 免费试用帐户](https://azure.microsoft.com/free/?WT.mc_id=A443DD604)。</span><span class="sxs-lookup"><span data-stu-id="20f87-120">[free Azure trial account](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).</span></span> <span data-ttu-id="20f87-121">有关如何将 Visual Studio web 项目部署到 Azure 应用服务 Web 应用的信息，请参阅[使用 Visual Studio 的 ASP.NET Web 部署](../../deployment/visual-studio-web-deployment/introduction.md)系列。</span><span class="sxs-lookup"><span data-stu-id="20f87-121">For information about how to deploy a Visual Studio web project to Azure App Service Web Apps, see the [ASP.NET Web Deployment using Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) series.</span></span> <span data-ttu-id="20f87-122">该教程还演示如何使用 Entity Framework Code First 迁移将 SQL Server 数据库部署到 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="20f87-122">That tutorial also shows how to use Entity Framework Code First Migrations to deploy your SQL Server database to Azure SQL Database.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="20f87-123">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="20f87-123">Software versions used in the tutorial</span></span>
> 
> - <span data-ttu-id="20f87-124">Microsoft Visual Studio 2017 或 Microsoft Visual Studio Community 2017</span><span class="sxs-lookup"><span data-stu-id="20f87-124">Microsoft Visual Studio 2017 or Microsoft Visual Studio Community 2017</span></span>
>   
> <span data-ttu-id="20f87-125">本教程还适用于 Visual Studio 2012 和 Visual Studio 2013 中，但有一些用户界面和项目模板中的差异。</span><span class="sxs-lookup"><span data-stu-id="20f87-125">This tutorial also works with Visual Studio 2012 and Visual Studio 2013, but there are some differences in the user interface and project template.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="20f87-126">你将生成</span><span class="sxs-lookup"><span data-stu-id="20f87-126">What you'll build</span></span>

<span data-ttu-id="20f87-127">在本教程中，你将：</span><span class="sxs-lookup"><span data-stu-id="20f87-127">In this tutorial, you'll:</span></span>

* <span data-ttu-id="20f87-128">生成与注册的课程的学生反映一所大学的数据对象</span><span class="sxs-lookup"><span data-stu-id="20f87-128">Build data objects that reflect a university with students enrolled in courses</span></span>
* <span data-ttu-id="20f87-129">生成从对象的数据库表</span><span class="sxs-lookup"><span data-stu-id="20f87-129">Build database tables from the objects</span></span>
* <span data-ttu-id="20f87-130">使用测试数据填充数据库</span><span class="sxs-lookup"><span data-stu-id="20f87-130">Populate the database with test data</span></span>
* <span data-ttu-id="20f87-131">在 web 窗体中显示数据</span><span class="sxs-lookup"><span data-stu-id="20f87-131">Display data in a web form</span></span>

## <a name="create-the-project"></a><span data-ttu-id="20f87-132">创建项目</span><span class="sxs-lookup"><span data-stu-id="20f87-132">Create the project</span></span>

1. <span data-ttu-id="20f87-133">在 Visual Studio 2017 中，创建**ASP.NET Web 应用程序 (.NET Framework)** 名为项目**ContosoUniversityModelBinding**。</span><span class="sxs-lookup"><span data-stu-id="20f87-133">In Visual Studio 2017, create a **ASP.NET Web Application (.NET Framework)** project called **ContosoUniversityModelBinding**.</span></span>

   ![创建项目](retrieving-data/_static/image19.png)

2. <span data-ttu-id="20f87-135">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="20f87-135">Select **OK**.</span></span> <span data-ttu-id="20f87-136">会显示对话框以选择一个模板。</span><span class="sxs-lookup"><span data-stu-id="20f87-136">The dialog box to select a template appears.</span></span>

   ![选择 web 窗体](retrieving-data/_static/image3.png)

3. <span data-ttu-id="20f87-138">选择**Web 窗体**模板。</span><span class="sxs-lookup"><span data-stu-id="20f87-138">Select the **Web Forms** template.</span></span> 

4. <span data-ttu-id="20f87-139">如有必要，更改到身份验证**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="20f87-139">If necessary, change the authentication to **Individual User Accounts**.</span></span> 

5. <span data-ttu-id="20f87-140">选择“确定”创建项目。</span><span class="sxs-lookup"><span data-stu-id="20f87-140">Select **OK** to create the project.</span></span>

## <a name="modify-site-appearance"></a><span data-ttu-id="20f87-141">修改站点外观</span><span class="sxs-lookup"><span data-stu-id="20f87-141">Modify site appearance</span></span>

   <span data-ttu-id="20f87-142">进行一些更改，以自定义站点外观。</span><span class="sxs-lookup"><span data-stu-id="20f87-142">Make a few changes to customize site appearance.</span></span> 
   
   1. <span data-ttu-id="20f87-143">打开 Site.Master 文件。</span><span class="sxs-lookup"><span data-stu-id="20f87-143">Open the Site.Master file.</span></span>
   
   2. <span data-ttu-id="20f87-144">更改显示的标题**Contoso University**而不**My ASP.NET Application**。</span><span class="sxs-lookup"><span data-stu-id="20f87-144">Change the title to display **Contoso University** and not **My ASP.NET Application**.</span></span>

      [!code-aspx-csharp[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

   3. <span data-ttu-id="20f87-145">更改中的标头文本**应用程序名称**到**Contoso University**。</span><span class="sxs-lookup"><span data-stu-id="20f87-145">Change the header text from **Application name** to **Contoso University**.</span></span>

      [!code-aspx-csharp[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

   4. <span data-ttu-id="20f87-146">更改导航标题链接到合适的站点。</span><span class="sxs-lookup"><span data-stu-id="20f87-146">Change the navigation header links to site appropriate ones.</span></span> 
   
      <span data-ttu-id="20f87-147">删除的链接**有关**并**联系人**并改为，将链接到**学生**页上，您将创建。</span><span class="sxs-lookup"><span data-stu-id="20f87-147">Remove the links for **About** and **Contact** and, instead, link to a **Students** page, which you will create.</span></span>

      [!code-aspx-csharp[Main](retrieving-data/samples/sample3.aspx)]

   5. <span data-ttu-id="20f87-148">保存 Site.Master。</span><span class="sxs-lookup"><span data-stu-id="20f87-148">Save Site.Master.</span></span>

## <a name="add-a-web-form-to-display-student-data"></a><span data-ttu-id="20f87-149">添加 web 窗体以显示学生数据</span><span class="sxs-lookup"><span data-stu-id="20f87-149">Add a web form to display student data</span></span>

   1. <span data-ttu-id="20f87-150">在中**解决方案资源管理器**，右键单击项目，选择**添加**，然后**新项**。</span><span class="sxs-lookup"><span data-stu-id="20f87-150">In **Solution Explorer**, right-click your project, select **Add** and then **New Item**.</span></span> 
   
   2. <span data-ttu-id="20f87-151">在中**添加新项**对话框中，选择**包含母版页的 Web 窗体**模板并将其命名**Students.aspx**。</span><span class="sxs-lookup"><span data-stu-id="20f87-151">In the **Add New Item** dialog box, select the **Web Form with Master Page** template and name it **Students.aspx**.</span></span>

      ![创建页](retrieving-data/_static/image5.png)

   3. <span data-ttu-id="20f87-153">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="20f87-153">Select **Add**.</span></span>
   
   4. <span data-ttu-id="20f87-154">对于 web 窗体的主页上，选择**Site.Master**。</span><span class="sxs-lookup"><span data-stu-id="20f87-154">For the web form's master page, select **Site.Master**.</span></span>
   
   5. <span data-ttu-id="20f87-155">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="20f87-155">Select **OK**.</span></span>
   

## <a name="add-the-data-model"></a><span data-ttu-id="20f87-156">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="20f87-156">Add the data model</span></span>

<span data-ttu-id="20f87-157">在中**模型**文件夹中，添加一个名为类**UniversityModels.cs**。</span><span class="sxs-lookup"><span data-stu-id="20f87-157">In the **Models** folder, add a class named **UniversityModels.cs**.</span></span>

   1. <span data-ttu-id="20f87-158">右键单击**模型**，选择**添加**，然后**新项**。</span><span class="sxs-lookup"><span data-stu-id="20f87-158">Right-click **Models**, select **Add**, and then **New Item**.</span></span> <span data-ttu-id="20f87-159">“添加新项”对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="20f87-159">The **Add New Item** dialog box appears.</span></span>

   2. <span data-ttu-id="20f87-160">从左侧的导航菜单中选择**代码**，然后**类**。</span><span class="sxs-lookup"><span data-stu-id="20f87-160">From the left navigation menu, select **Code**, then **Class**.</span></span>

      ![创建模型类](retrieving-data/_static/image20.png)

   3. <span data-ttu-id="20f87-162">将类命名**UniversityModels.cs** ，然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="20f87-162">Name the class **UniversityModels.cs** and select **Add**.</span></span>

      <span data-ttu-id="20f87-163">在此文件中，定义`SchoolContext`， `Student`， `Enrollment`，和`Course`类，如下所示：</span><span class="sxs-lookup"><span data-stu-id="20f87-163">In this file, define the `SchoolContext`, `Student`, `Enrollment`, and `Course` classes as follows:</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample4.cs)]

      <span data-ttu-id="20f87-164">`SchoolContext`类派生自`DbContext`，这会管理数据库连接并更改数据中。</span><span class="sxs-lookup"><span data-stu-id="20f87-164">The `SchoolContext` class derives from `DbContext`, which manages the database connection and changes in the data.</span></span>

      <span data-ttu-id="20f87-165">在中`Student`类中，请注意，应用于的特性`FirstName`， `LastName`，和`Year`属性。</span><span class="sxs-lookup"><span data-stu-id="20f87-165">In the `Student` class, notice the attributes applied to the `FirstName`, `LastName`, and `Year` properties.</span></span> <span data-ttu-id="20f87-166">本教程中使用这些属性进行数据验证。</span><span class="sxs-lookup"><span data-stu-id="20f87-166">This tutorial uses these attributes for data validation.</span></span> <span data-ttu-id="20f87-167">若要简化代码，只对这些属性用数据验证特性进行标记。</span><span class="sxs-lookup"><span data-stu-id="20f87-167">To simplify the code, only these properties are marked with data-validation attributes.</span></span> <span data-ttu-id="20f87-168">在实际项目中，会将验证特性应用到需要验证的所有属性。</span><span class="sxs-lookup"><span data-stu-id="20f87-168">In a real project, you would apply validation attributes to all properties needing validation.</span></span>

   4. <span data-ttu-id="20f87-169">保存 UniversityModels.cs。</span><span class="sxs-lookup"><span data-stu-id="20f87-169">Save UniversityModels.cs.</span></span>

## <a name="set-up-the-database-based-on-classes"></a><span data-ttu-id="20f87-170">将基于类数据库设置</span><span class="sxs-lookup"><span data-stu-id="20f87-170">Set up the database based on classes</span></span>

<span data-ttu-id="20f87-171">本教程使用[Code First 迁移](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/)创建对象和数据库表。</span><span class="sxs-lookup"><span data-stu-id="20f87-171">This tutorial uses [Code First Migrations](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/) to create objects and database tables.</span></span> <span data-ttu-id="20f87-172">这些表存储有关学生和他们的课程的信息。</span><span class="sxs-lookup"><span data-stu-id="20f87-172">These tables store information about the students and their courses.</span></span>

   1. <span data-ttu-id="20f87-173">选择**工具** > **NuGet 包管理器** > **程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="20f87-173">Select **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

   2. <span data-ttu-id="20f87-174">在中**程序包管理器控制台**，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="20f87-174">In **Package Manager Console**, run this command:</span></span>  
      `enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

      <span data-ttu-id="20f87-175">如果命令成功完成，会显示一条消息表明已启用迁移。</span><span class="sxs-lookup"><span data-stu-id="20f87-175">If the command completes successfully, a message stating migrations have been enabled appears.</span></span>

      ![启用迁移](retrieving-data/_static/image8.png)

      <span data-ttu-id="20f87-177">请注意，文件命名为*Configuration.cs*已创建。</span><span class="sxs-lookup"><span data-stu-id="20f87-177">Notice that a file named *Configuration.cs* has been created.</span></span> <span data-ttu-id="20f87-178">`Configuration`类具有`Seed`方法，可以预先填充测试数据与数据库表。</span><span class="sxs-lookup"><span data-stu-id="20f87-178">The `Configuration` class has a `Seed` method, which can pre-populate the database tables with test data.</span></span>

## <a name="pre-populate-the-database"></a><span data-ttu-id="20f87-179">在数据库中预先填充</span><span class="sxs-lookup"><span data-stu-id="20f87-179">Pre-populate the database</span></span>

   1. <span data-ttu-id="20f87-180">打开 Configuration.cs。</span><span class="sxs-lookup"><span data-stu-id="20f87-180">Open Configuration.cs.</span></span>
   
   2. <span data-ttu-id="20f87-181">将以下代码添加到 `Seed` 方法中。</span><span class="sxs-lookup"><span data-stu-id="20f87-181">Add the following code to the `Seed` method.</span></span> <span data-ttu-id="20f87-182">此外，添加`using`语句`ContosoUniversityModelBinding. Models`命名空间。</span><span class="sxs-lookup"><span data-stu-id="20f87-182">Also, add a `using` statement for the `ContosoUniversityModelBinding. Models` namespace.</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample5.cs)]

   3. <span data-ttu-id="20f87-183">保存 Configuration.cs。</span><span class="sxs-lookup"><span data-stu-id="20f87-183">Save Configuration.cs.</span></span>

   4. <span data-ttu-id="20f87-184">在包管理器控制台中，运行命令**添加迁移初始**。</span><span class="sxs-lookup"><span data-stu-id="20f87-184">In the Package Manager Console, run the command **add-migration initial**.</span></span>

   5. <span data-ttu-id="20f87-185">运行命令**更新数据库**。</span><span class="sxs-lookup"><span data-stu-id="20f87-185">Run the command **update-database**.</span></span>

      <span data-ttu-id="20f87-186">如果运行此命令时收到异常`StudentID`并`CourseID`的值可能不同于`Seed`方法的值。</span><span class="sxs-lookup"><span data-stu-id="20f87-186">If you receive an exception when running this command, the `StudentID` and `CourseID` values might be different from the `Seed` method values.</span></span> <span data-ttu-id="20f87-187">打开这些数据库表，并找到的现有值`StudentID`和`CourseID`。</span><span class="sxs-lookup"><span data-stu-id="20f87-187">Open those database tables and find existing values for `StudentID` and `CourseID`.</span></span> <span data-ttu-id="20f87-188">将这些值添加到为种子设定代码`Enrollments`表。</span><span class="sxs-lookup"><span data-stu-id="20f87-188">Add those values to the code for seeding the `Enrollments` table.</span></span>

## <a name="add-a-gridview-control"></a><span data-ttu-id="20f87-189">添加 GridView 控件</span><span class="sxs-lookup"><span data-stu-id="20f87-189">Add a GridView control</span></span>

<span data-ttu-id="20f87-190">使用填充的数据库数据，现在，你准备好检索该数据并将其显示。</span><span class="sxs-lookup"><span data-stu-id="20f87-190">With populated database data, you're now ready to retrieve that data and display it.</span></span> 

1. <span data-ttu-id="20f87-191">打开 Students.aspx。</span><span class="sxs-lookup"><span data-stu-id="20f87-191">Open Students.aspx.</span></span>

2. <span data-ttu-id="20f87-192">找到`MainContent`占位符。</span><span class="sxs-lookup"><span data-stu-id="20f87-192">Locate the `MainContent` placeholder.</span></span> <span data-ttu-id="20f87-193">该占位符，在添加**GridView**包括此代码的控制。</span><span class="sxs-lookup"><span data-stu-id="20f87-193">Within that placeholder, add a **GridView** control that includes this code.</span></span>

   [!code-aspx-csharp[Main](retrieving-data/samples/sample6.aspx)]

   <span data-ttu-id="20f87-194">需要注意的事项：</span><span class="sxs-lookup"><span data-stu-id="20f87-194">Things to note:</span></span>
   * <span data-ttu-id="20f87-195">请注意，值设置为`SelectMethod`GridView 元素中的属性。</span><span class="sxs-lookup"><span data-stu-id="20f87-195">Notice the value set for the `SelectMethod` property in the GridView element.</span></span> <span data-ttu-id="20f87-196">此值指定用于检索下一步中创建的 GridView 数据的方法。</span><span class="sxs-lookup"><span data-stu-id="20f87-196">This value specifies the method used to retrieve GridView data, which you create in the next step.</span></span> 
   
   * <span data-ttu-id="20f87-197">`ItemType`属性设置为`Student`前面创建的类。</span><span class="sxs-lookup"><span data-stu-id="20f87-197">The `ItemType` property is set to the `Student` class created earlier.</span></span> <span data-ttu-id="20f87-198">此设置，可以引用的标记中的类属性。</span><span class="sxs-lookup"><span data-stu-id="20f87-198">This setting allows you to reference class properties in the markup.</span></span> <span data-ttu-id="20f87-199">例如，`Student`类具有一个名为集合`Enrollments`。</span><span class="sxs-lookup"><span data-stu-id="20f87-199">For example, the `Student` class has a collection named `Enrollments`.</span></span> <span data-ttu-id="20f87-200">可以使用`Item.Enrollments`检索该集合，然后使用[LINQ 语法](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)若要检索每个学生的注册信用额度之和。</span><span class="sxs-lookup"><span data-stu-id="20f87-200">You can use `Item.Enrollments` to retrieve that collection and then use [LINQ syntax](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) to retrieve each student's enrolled credits sum.</span></span>
   
3. <span data-ttu-id="20f87-201">保存 Students.aspx。</span><span class="sxs-lookup"><span data-stu-id="20f87-201">Save Students.aspx.</span></span>

## <a name="add-code-to-retrieve-data"></a><span data-ttu-id="20f87-202">添加代码以检索数据</span><span class="sxs-lookup"><span data-stu-id="20f87-202">Add code to retrieve data</span></span>

   <span data-ttu-id="20f87-203">在 Students.aspx 代码隐藏文件中，将为指定的方法添加`SelectMethod`值。</span><span class="sxs-lookup"><span data-stu-id="20f87-203">In the Students.aspx code-behind file, add the method specified for the `SelectMethod` value.</span></span> 
   
   1. <span data-ttu-id="20f87-204">打开 Students.aspx.cs。</span><span class="sxs-lookup"><span data-stu-id="20f87-204">Open Students.aspx.cs.</span></span>
   
   2. <span data-ttu-id="20f87-205">添加`using`语句`ContosoUniversityModelBinding. Models`和`System.Data.Entity`命名空间。</span><span class="sxs-lookup"><span data-stu-id="20f87-205">Add `using` statements for the `ContosoUniversityModelBinding. Models` and `System.Data.Entity` namespaces.</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample7.cs)]

   3. <span data-ttu-id="20f87-206">将为指定的方法添加`SelectMethod`:</span><span class="sxs-lookup"><span data-stu-id="20f87-206">Add the method you specified for `SelectMethod`:</span></span>

      [!code-csharp[Main](retrieving-data/samples/sample8.cs)]

      <span data-ttu-id="20f87-207">`Include`子句可以提高查询性能，但不是必需的。</span><span class="sxs-lookup"><span data-stu-id="20f87-207">The `Include` clause improves query performance but isn't required.</span></span> <span data-ttu-id="20f87-208">无需`Include`子句中，数据使用检索[*延迟加载*](https://en.wikipedia.org/wiki/Lazy_loading)，其中涉及每次向数据库发送一个单独的查询相关的检索数据。</span><span class="sxs-lookup"><span data-stu-id="20f87-208">Without the `Include` clause, the data is retrieved using [*lazy loading*](https://en.wikipedia.org/wiki/Lazy_loading), which involves sending a separate query to the database each time related data is retrieved.</span></span> <span data-ttu-id="20f87-209">与`Include`子句中，数据使用检索*预先加载*，这意味着单一数据库查询检索所有相关的数据。</span><span class="sxs-lookup"><span data-stu-id="20f87-209">With the `Include` clause, data is retrieved using *eager loading*, which means a single database query retrieves all related data.</span></span> <span data-ttu-id="20f87-210">如果不使用相关的数据，预先加载是效率较低，因为检索更多的数据。</span><span class="sxs-lookup"><span data-stu-id="20f87-210">If related data isn't used, eager loading is less efficient because more data is retrieved.</span></span> <span data-ttu-id="20f87-211">但是，在这种情况下，预先加载提供最佳性能，因为相关的数据显示为每个记录。</span><span class="sxs-lookup"><span data-stu-id="20f87-211">However, in this case, eager loading gives you the best performance because the related data is displayed for each record.</span></span>

      <span data-ttu-id="20f87-212">有关加载时的性能注意事项的详细信息相关的数据，请参阅**延迟、 预先和显式加载相关数据的**主题中[使用实体框架在 ASP.NET 中读取相关数据MVC 应用程序](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)一文。</span><span class="sxs-lookup"><span data-stu-id="20f87-212">For more information about performance considerations when loading related data, see the **Lazy, Eager, and Explicit Loading of Related Data** section in the [Reading Related Data with the Entity Framework in an ASP.NET MVC Application](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) article.</span></span>

      <span data-ttu-id="20f87-213">默认情况下，数据按标记为键属性的值。</span><span class="sxs-lookup"><span data-stu-id="20f87-213">By default, the data is sorted by the values of the property marked as the key.</span></span> <span data-ttu-id="20f87-214">您可以添加`OrderBy`子句指定不同的排序值。</span><span class="sxs-lookup"><span data-stu-id="20f87-214">You can add an `OrderBy` clause to specify a different sort value.</span></span> <span data-ttu-id="20f87-215">在此示例中，默认值`StudentID`使用属性进行排序。</span><span class="sxs-lookup"><span data-stu-id="20f87-215">In this example, the default `StudentID` property is used for sorting.</span></span> <span data-ttu-id="20f87-216">在中[排序、 分页和筛选数据](sorting-paging-and-filtering-data.md)文章中，用户能够选择列进行排序。</span><span class="sxs-lookup"><span data-stu-id="20f87-216">In the [Sorting, Paging, and Filtering Data](sorting-paging-and-filtering-data.md) article, the user is enabled to select a column for sorting.</span></span>
 
   4. <span data-ttu-id="20f87-217">保存 Students.aspx.cs。</span><span class="sxs-lookup"><span data-stu-id="20f87-217">Save Students.aspx.cs.</span></span>

## <a name="run-your-application"></a><span data-ttu-id="20f87-218">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="20f87-218">Run your application</span></span> 

<span data-ttu-id="20f87-219">运行 web 应用程序 (**F5**)，并导航到**学生**页，该页显示以下信息：</span><span class="sxs-lookup"><span data-stu-id="20f87-219">Run your web application (**F5**) and navigate to the **Students** page, which displays the following:</span></span>

   ![显示数据](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a><span data-ttu-id="20f87-221">自动生成的模型绑定方法</span><span class="sxs-lookup"><span data-stu-id="20f87-221">Automatic generation of model binding methods</span></span>

<span data-ttu-id="20f87-222">学习本系列教程中，当您可以只需复制代码从本教程到你的项目。</span><span class="sxs-lookup"><span data-stu-id="20f87-222">When working through this tutorial series, you can simply copy the code from the tutorial to your project.</span></span> <span data-ttu-id="20f87-223">但是，此方法的一个缺点是功能的，您可能会意识到的 Visual Studio 以自动生成的模型绑定方法代码提供。</span><span class="sxs-lookup"><span data-stu-id="20f87-223">However, one disadvantage of this approach is that you may not become aware of the feature provided by Visual Studio to automatically generate code for model binding methods.</span></span> <span data-ttu-id="20f87-224">您自己的项目上工作时，自动代码生成可以节省时间并获得如何实现的操作的某种意义上的帮助。</span><span class="sxs-lookup"><span data-stu-id="20f87-224">When working on your own projects, automatic code generation can save you time and help you gain a sense of how to implement an operation.</span></span> <span data-ttu-id="20f87-225">本部分介绍自动代码生成功能。</span><span class="sxs-lookup"><span data-stu-id="20f87-225">This section describes the automatic code generation feature.</span></span> <span data-ttu-id="20f87-226">本部分只是信息性并不包含任何您需要在项目中实现的代码。</span><span class="sxs-lookup"><span data-stu-id="20f87-226">This section is only informational and does not contain any code you need to implement in your project.</span></span> 

<span data-ttu-id="20f87-227">当设置为值`SelectMethod`， `UpdateMethod`， `InsertMethod`，或`DeleteMethod`标记代码中的属性，可以选择**创建的新方法**选项。</span><span class="sxs-lookup"><span data-stu-id="20f87-227">When setting a value for the `SelectMethod`, `UpdateMethod`, `InsertMethod`, or `DeleteMethod` properties in the markup code, you can select the **Create New Method** option.</span></span>

![创建一个方法](retrieving-data/_static/image18.png)

<span data-ttu-id="20f87-229">Visual Studio 不仅具有正确签名的代码隐藏中创建一个方法，而且还会生成实现代码，以执行该操作。</span><span class="sxs-lookup"><span data-stu-id="20f87-229">Visual Studio not only creates a method in the code-behind with the proper signature, but also generates implementation code to perform the operation.</span></span> <span data-ttu-id="20f87-230">如果首次设置`ItemType`属性前使用的自动代码生成功能，生成的代码使用该类型的操作。</span><span class="sxs-lookup"><span data-stu-id="20f87-230">If you first set the `ItemType` property before using the automatic code generation feature, the generated code uses that type for the operations.</span></span> <span data-ttu-id="20f87-231">例如，当设置`UpdateMethod`自动生成属性，以下代码：</span><span class="sxs-lookup"><span data-stu-id="20f87-231">For example, when setting the `UpdateMethod` property, the following code is automatically generated:</span></span>

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

<span data-ttu-id="20f87-232">同样，此代码不需要添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="20f87-232">Again, this code doesn't need to be added to your project.</span></span> <span data-ttu-id="20f87-233">在下一步的教程中，将实现用于更新、 删除和添加新数据的方法。</span><span class="sxs-lookup"><span data-stu-id="20f87-233">In the next tutorial, you'll implement methods for updating, deleting, and adding new data.</span></span>

## <a name="summary"></a><span data-ttu-id="20f87-234">总结</span><span class="sxs-lookup"><span data-stu-id="20f87-234">Summary</span></span>

<span data-ttu-id="20f87-235">在本教程中，将创建数据模型类，并基于这些类生成数据库。</span><span class="sxs-lookup"><span data-stu-id="20f87-235">In this tutorial, you created data model classes and generated a database from those classes.</span></span> <span data-ttu-id="20f87-236">使用测试数据填充数据库表。</span><span class="sxs-lookup"><span data-stu-id="20f87-236">You filled the database tables with test data.</span></span> <span data-ttu-id="20f87-237">模型绑定用于从数据库检索数据，然后在 GridView 中显示数据。</span><span class="sxs-lookup"><span data-stu-id="20f87-237">You used model binding to retrieve data from the database, and then displayed the data in a GridView.</span></span>

<span data-ttu-id="20f87-238">在接下来[教程](updating-deleting-and-creating-data.md)在此系列中，则将启用更新、 删除和创建数据。</span><span class="sxs-lookup"><span data-stu-id="20f87-238">In the next [tutorial](updating-deleting-and-creating-data.md) in this series, you'll enable updating, deleting, and creating data.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="20f87-239">下一页</span><span class="sxs-lookup"><span data-stu-id="20f87-239">Next</span></span>](updating-deleting-and-creating-data.md)
