---
uid: mvc/overview/getting-started/database-first-development/setting-up-database
title: 教程：开始使用 EF Database First 通过 MVC 5
description: 本教程介绍如何开始使用现有数据库并快速创建 web 应用程序，使用户能够与数据交互。
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 095abad4-3bfe-4f06-b092-ae6a735b7e49
msc.legacyurl: /mvc/overview/getting-started/database-first-development/setting-up-database
msc.type: authoredcontent
ms.openlocfilehash: a503e3db63c873249178fd4783d322f4067c3208
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236375"
---
# <a name="tutorial-get-started-with-ef-database-first-using-mvc-5"></a><span data-ttu-id="200e5-103">教程：开始使用 EF Database First 通过 MVC 5</span><span class="sxs-lookup"><span data-stu-id="200e5-103">Tutorial: Get started with EF Database First using MVC 5</span></span>

<span data-ttu-id="200e5-104">使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="200e5-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="200e5-105">本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="200e5-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="200e5-106">生成的代码对应于数据库表中的列。</span><span class="sxs-lookup"><span data-stu-id="200e5-106">The generated code corresponds to the columns in the database table.</span></span> <span data-ttu-id="200e5-107">在本系列的最后部分，会将站点和数据库部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="200e5-107">In the last part of the series, you will deploy the site and database to Azure.</span></span>

<span data-ttu-id="200e5-108">本教程介绍如何开始使用现有数据库并快速创建 web 应用程序，使用户能够与数据交互。</span><span class="sxs-lookup"><span data-stu-id="200e5-108">This tutorial shows how to start with an existing database and quickly create a web application that enables users to interact with the data.</span></span> <span data-ttu-id="200e5-109">它使用 Entity Framework 6 和 MVC 5 构建 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="200e5-109">It uses the Entity Framework 6 and MVC 5 to build the web application.</span></span> <span data-ttu-id="200e5-110">ASP.NET 基架功能可以自动生成用于显示、 更新、 创建和删除数据的代码。</span><span class="sxs-lookup"><span data-stu-id="200e5-110">The ASP.NET Scaffolding feature enables you to automatically generate code for displaying, updating, creating and deleting data.</span></span> <span data-ttu-id="200e5-111">使用 Visual Studio 中的发布工具，你可以轻松部署站点和数据库到 Azure。</span><span class="sxs-lookup"><span data-stu-id="200e5-111">Using the publishing tools within Visual Studio, you can easily deploy the site and database to Azure.</span></span>

<span data-ttu-id="200e5-112">本系列的此部分主要介绍在创建数据库和使用数据填充。</span><span class="sxs-lookup"><span data-stu-id="200e5-112">This part of the series focuses on creating a database and populating it with data.</span></span>

<span data-ttu-id="200e5-113">这一系列已用的发布内容写入 Tom Dykstra 和 Rick Anderson。</span><span class="sxs-lookup"><span data-stu-id="200e5-113">This series was written with contributions from Tom Dykstra and Rick Anderson.</span></span> <span data-ttu-id="200e5-114">它改进了基于注释部分中的用户的反馈。</span><span class="sxs-lookup"><span data-stu-id="200e5-114">It was improved based on feedback from users in the comments section.</span></span>

<span data-ttu-id="200e5-115">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="200e5-115">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="200e5-116">将数据库设置</span><span class="sxs-lookup"><span data-stu-id="200e5-116">Set up the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="200e5-117">系统必备</span><span class="sxs-lookup"><span data-stu-id="200e5-117">Prerequisites</span></span>

* [<span data-ttu-id="200e5-118">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="200e5-118">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

## <a name="introduction"></a><span data-ttu-id="200e5-119">介绍</span><span class="sxs-lookup"><span data-stu-id="200e5-119">Introduction</span></span>

<span data-ttu-id="200e5-120">本教程探讨这种情况，如果有数据库并且想要为 web 应用程序根据该数据库的字段生成代码。</span><span class="sxs-lookup"><span data-stu-id="200e5-120">This tutorial addresses the situation where you have a database and want to generate code for a web application based on the fields of that database.</span></span> <span data-ttu-id="200e5-121">这种方法称为数据库优先开发。</span><span class="sxs-lookup"><span data-stu-id="200e5-121">This approach is called Database First development.</span></span> <span data-ttu-id="200e5-122">如果你还没有现有数据库，则可以改用方法称作 Code First 开发这涉及到定义数据类和类属性从生成数据库。</span><span class="sxs-lookup"><span data-stu-id="200e5-122">If you do not already have an existing database, you can instead use an approach called Code First development which involves defining data classes and generating the database from the class properties.</span></span>

## <a name="set-up-the-database"></a><span data-ttu-id="200e5-123">将数据库设置</span><span class="sxs-lookup"><span data-stu-id="200e5-123">Set up the database</span></span>

<span data-ttu-id="200e5-124">以模拟拥有现有的数据库的环境，你将首先使用一些预填充的数据，创建一个数据库，然后创建 web 应用程序连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="200e5-124">To mimic the environment of having an existing database, you will first create a database with some pre-filled data, and then create your web application that connects to the database.</span></span>

<span data-ttu-id="200e5-125">本教程是使用 LocalDB 开发的。</span><span class="sxs-lookup"><span data-stu-id="200e5-125">This tutorial was developed using LocalDB.</span></span> <span data-ttu-id="200e5-126">您可以使用现有的数据库服务器，而不是 LocalDB，但具体取决于版本的 Visual Studio 和您的数据库的类型，所有 Visual Studio 中的数据工具可能不受支持。</span><span class="sxs-lookup"><span data-stu-id="200e5-126">You can use an existing database server instead of LocalDB, but depending on your version of Visual Studio and your type of database, all of the data tools in Visual Studio might not be supported.</span></span> <span data-ttu-id="200e5-127">如果这些工具不可用于你的数据库，您可能需要执行某些管理套件中的特定于数据库的步骤为你的数据库。</span><span class="sxs-lookup"><span data-stu-id="200e5-127">If the tools are not available for your database, you may need to perform some of the database-specific steps within the management suite for your database.</span></span>

<span data-ttu-id="200e5-128">如果你的 Visual Studio 版本中有数据库工具的问题，请确保已安装的数据库工具的最新版本。</span><span class="sxs-lookup"><span data-stu-id="200e5-128">If you have a problem with the database tools in your version of Visual Studio, make sure you have installed the latest version of the database tools.</span></span> <span data-ttu-id="200e5-129">有关更新或安装数据库工具的信息，请参阅[Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/hh297027)。</span><span class="sxs-lookup"><span data-stu-id="200e5-129">For information about updating or installing the database tools, see [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/hh297027).</span></span>

<span data-ttu-id="200e5-130">启动 Visual Studio 并创建**SQL Server 数据库项目**。</span><span class="sxs-lookup"><span data-stu-id="200e5-130">Launch Visual Studio and create a **SQL Server Database Project**.</span></span> <span data-ttu-id="200e5-131">将项目命名**ContosoUniversityData**。</span><span class="sxs-lookup"><span data-stu-id="200e5-131">Name the project **ContosoUniversityData**.</span></span>

![创建数据库项目](setting-up-database/_static/image1.png)

<span data-ttu-id="200e5-133">您现在有一个空数据库项目。</span><span class="sxs-lookup"><span data-stu-id="200e5-133">You now have an empty database project.</span></span> <span data-ttu-id="200e5-134">您将此数据库在部署到 Azure 以后本教程中，因此将需要将 Azure SQL Database 设置为项目的目标平台。</span><span class="sxs-lookup"><span data-stu-id="200e5-134">You will deploy this database to Azure later in this tutorial, so you'll need to set Azure SQL Database as the target platform for the project.</span></span> <span data-ttu-id="200e5-135">设置目标平台不实际部署该数据库。它只意味着数据库项目将验证数据库设计为与目标平台兼容。</span><span class="sxs-lookup"><span data-stu-id="200e5-135">Setting the target platform does not actually deploy the database; it only means that the database project will verify that the database design is compatible with the target platform.</span></span> <span data-ttu-id="200e5-136">若要设置目标平台，打开**属性**项目并选择**Microsoft Azure SQL 数据库**为目标平台。</span><span class="sxs-lookup"><span data-stu-id="200e5-136">To set the target platform, open the **Properties** for the project and select **Microsoft Azure SQL Database** for the target platform.</span></span>

<span data-ttu-id="200e5-137">可以创建本教程中添加的定义的表的 SQL 脚本所需的表。</span><span class="sxs-lookup"><span data-stu-id="200e5-137">You can create the tables needed for this tutorial by adding SQL scripts that define the tables.</span></span> <span data-ttu-id="200e5-138">右键单击项目并添加新项。</span><span class="sxs-lookup"><span data-stu-id="200e5-138">Right-click your project and add a new item.</span></span> <span data-ttu-id="200e5-139">选择**表和视图** > **表**并将其命名*学生*。</span><span class="sxs-lookup"><span data-stu-id="200e5-139">Select **Tables and Views** > **Table** and name it *Student*.</span></span>

<span data-ttu-id="200e5-140">表文件中替换以下代码以创建表的 T-SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="200e5-140">In the table file, replace the T-SQL command with the following code to create the table.</span></span>

[!code-sql[Main](setting-up-database/samples/sample1.sql)]

<span data-ttu-id="200e5-141">请注意，设计窗口会自动同步的代码。</span><span class="sxs-lookup"><span data-stu-id="200e5-141">Notice that the design window automatically synchronizes with the code.</span></span> <span data-ttu-id="200e5-142">您可以使用代码或设计器。</span><span class="sxs-lookup"><span data-stu-id="200e5-142">You can work with either the code or designer.</span></span>

![显示代码和设计](setting-up-database/_static/image5.png)

<span data-ttu-id="200e5-144">添加另一个表。</span><span class="sxs-lookup"><span data-stu-id="200e5-144">Add another table.</span></span> <span data-ttu-id="200e5-145">这一次课程将其命名，并使用以下 T-SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="200e5-145">This time name it Course and use the following T-SQL command.</span></span>

[!code-sql[Main](setting-up-database/samples/sample2.sql)]

<span data-ttu-id="200e5-146">然后，重复一次，以创建名为注册的表。</span><span class="sxs-lookup"><span data-stu-id="200e5-146">And, repeat one more time to create a table named Enrollment.</span></span>

[!code-sql[Main](setting-up-database/samples/sample3.sql)]

<span data-ttu-id="200e5-147">您可以在用数据填充数据库通过部署数据库后运行的脚本。</span><span class="sxs-lookup"><span data-stu-id="200e5-147">You can populate your database with data through a script that is run after the database is deployed.</span></span> <span data-ttu-id="200e5-148">向项目添加后期部署脚本。</span><span class="sxs-lookup"><span data-stu-id="200e5-148">Add a Post-Deployment Script to the project.</span></span> <span data-ttu-id="200e5-149">右键单击项目并添加新项。</span><span class="sxs-lookup"><span data-stu-id="200e5-149">Right-click your project and add a new item.</span></span> <span data-ttu-id="200e5-150">选择**用户脚本** > **后期部署脚本**。</span><span class="sxs-lookup"><span data-stu-id="200e5-150">Select **User Scripts** > **Post-Deployment Script**.</span></span> <span data-ttu-id="200e5-151">可以使用默认名称。</span><span class="sxs-lookup"><span data-stu-id="200e5-151">You can use the default name.</span></span>

<span data-ttu-id="200e5-152">将以下 T-SQL 代码添加到后期部署脚本。</span><span class="sxs-lookup"><span data-stu-id="200e5-152">Add the following T-SQL code to the post-deployment script.</span></span> <span data-ttu-id="200e5-153">此脚本只需将数据添加到数据库时找到没有匹配的记录。</span><span class="sxs-lookup"><span data-stu-id="200e5-153">This script simply adds data to the database when no matching record is found.</span></span> <span data-ttu-id="200e5-154">不覆盖或删除您可能会输入到数据库的任何数据。</span><span class="sxs-lookup"><span data-stu-id="200e5-154">It does not overwrite or delete any data you may have entered into the database.</span></span>

[!code-sql[Main](setting-up-database/samples/sample4.sql)]

<span data-ttu-id="200e5-155">请务必注意每次部署您的数据库项目时，运行后期部署脚本。</span><span class="sxs-lookup"><span data-stu-id="200e5-155">It is important to note that the post-deployment script is run every time you deploy your database project.</span></span> <span data-ttu-id="200e5-156">因此，您需要编写此脚本时请仔细考虑您的要求。</span><span class="sxs-lookup"><span data-stu-id="200e5-156">Therefore, you need to carefully consider your requirements when writing this script.</span></span> <span data-ttu-id="200e5-157">在某些情况下，你可能想要从一组已知的数据开始，每次部署该项目。</span><span class="sxs-lookup"><span data-stu-id="200e5-157">In some cases, you may wish to start over from a known set of data every time the project is deployed.</span></span> <span data-ttu-id="200e5-158">在其他情况下，您可能不想要更改的现有数据以任何方式。</span><span class="sxs-lookup"><span data-stu-id="200e5-158">In other cases, you may not want to alter the existing data in any way.</span></span> <span data-ttu-id="200e5-159">根据您的要求，可以决定是否需要后期部署脚本或需要在脚本中包括。</span><span class="sxs-lookup"><span data-stu-id="200e5-159">Based on your requirements, you can decide whether you need a post-deployment script or what you need to include in the script.</span></span> <span data-ttu-id="200e5-160">有关填充使用后期部署脚本在数据库的详细信息，请参阅[SQL Server 数据库项目中包括数据](https://blogs.msdn.com/b/ssdt/archive/2012/02/02/including-data-in-an-sql-server-database-project.aspx)。</span><span class="sxs-lookup"><span data-stu-id="200e5-160">For more information about populating your database with a post-deployment script, see [Including Data in a SQL Server Database Project](https://blogs.msdn.com/b/ssdt/archive/2012/02/02/including-data-in-an-sql-server-database-project.aspx).</span></span>

<span data-ttu-id="200e5-161">现在，您有 4 个 SQL 脚本文件不存在实际的表。</span><span class="sxs-lookup"><span data-stu-id="200e5-161">You now have 4 SQL script files but no actual tables.</span></span> <span data-ttu-id="200e5-162">已准备好将您的数据库项目部署到 localdb。</span><span class="sxs-lookup"><span data-stu-id="200e5-162">You are ready to deploy your database project to localdb.</span></span> <span data-ttu-id="200e5-163">在 Visual Studio 中，单击开始按钮 （或 F5） 以生成和部署数据库项目。</span><span class="sxs-lookup"><span data-stu-id="200e5-163">In Visual Studio, click the Start button (or F5) to build and deploy your database project.</span></span> <span data-ttu-id="200e5-164">检查**输出**选项卡以验证生成和部署是否成功。</span><span class="sxs-lookup"><span data-stu-id="200e5-164">Check the **Output** tab to verify that the build and deployment succeeded.</span></span>

<span data-ttu-id="200e5-165">若要查看已创建新数据库，请打开**SQL Server 对象资源管理器**并查找正确的本地数据库服务器中的项目的名称 (在这种情况下 **(localdb) \ProjectsV13**)。</span><span class="sxs-lookup"><span data-stu-id="200e5-165">To see that the new database has been created, open **SQL Server Object Explorer** and look for the name of the project in the correct local database server (in this case **(localdb)\ProjectsV13**).</span></span>

<span data-ttu-id="200e5-166">若要查看表中填充了数据，请右键单击某个表，并选择**查看数据**。</span><span class="sxs-lookup"><span data-stu-id="200e5-166">To see that the tables are populated with data, right-click a table, and select **View Data**.</span></span>

![显示表数据](setting-up-database/_static/image9.png)

<span data-ttu-id="200e5-168">显示表数据的可编辑视图。</span><span class="sxs-lookup"><span data-stu-id="200e5-168">An editable view of the table data is displayed.</span></span> <span data-ttu-id="200e5-169">例如，如果您选择**表** > **dbo.course** > **查看数据**，请参阅具有三个列的表 (**课程**，**标题**，和**信用额度**) 和四个行。</span><span class="sxs-lookup"><span data-stu-id="200e5-169">For example, if you select **Tables** > **dbo.course** > **View Data**, you see a table with three columns (**Course**, **Title**, and **Credits**) and four rows.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="200e5-170">其他资源</span><span class="sxs-lookup"><span data-stu-id="200e5-170">Additional resources</span></span>

<span data-ttu-id="200e5-171">Code First 开发的介绍性示例，请参阅[Getting Started with ASP.NET MVC 5](../introduction/getting-started.md)。</span><span class="sxs-lookup"><span data-stu-id="200e5-171">For an introductory example of Code First development, see [Getting Started with ASP.NET MVC 5](../introduction/getting-started.md).</span></span> <span data-ttu-id="200e5-172">有关更高级的示例，请参阅[为 ASP.NET MVC 4 应用程序创建实体框架数据模型](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)。</span><span class="sxs-lookup"><span data-stu-id="200e5-172">For a more advanced example, see [Creating an Entity Framework Data Model for an ASP.NET MVC 4 App](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>

<span data-ttu-id="200e5-173">选择要使用的实体框架方法的指导，请参阅[实体框架开发方法](https://msdn.microsoft.com/library/ms178359.aspx#dbfmfcf)。</span><span class="sxs-lookup"><span data-stu-id="200e5-173">For guidance on selecting which Entity Framework approach to use, see [Entity Framework Development Approaches](https://msdn.microsoft.com/library/ms178359.aspx#dbfmfcf).</span></span>

## <a name="next-steps"></a><span data-ttu-id="200e5-174">后续步骤</span><span class="sxs-lookup"><span data-stu-id="200e5-174">Next steps</span></span>

<span data-ttu-id="200e5-175">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="200e5-175">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="200e5-176">将数据库设置</span><span class="sxs-lookup"><span data-stu-id="200e5-176">Set up the database</span></span>

<span data-ttu-id="200e5-177">转到下一步的教程，了解如何创建 web 应用程序和数据模型。</span><span class="sxs-lookup"><span data-stu-id="200e5-177">Advance to the next tutorial to learn how to create the web application and data models.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="200e5-178">创建 web 应用程序和数据模型</span><span class="sxs-lookup"><span data-stu-id="200e5-178">Create the web application and data models</span></span>](creating-the-web-application.md)