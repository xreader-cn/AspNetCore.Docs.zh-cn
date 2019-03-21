---
title: ASP.NET Core 中的 Razor 页面和 EF Core - 数据模型 - 第 5 个教程（共 8 个）
author: rick-anderson
description: 本教程将添加更多实体和关系，并通过指定格式设置、验证和映射规则来自定义数据模型。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: fbe43e019ddab6f9acc2ea46799f0a39aa7c2e7c
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208985"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="99e0d-103">ASP.NET Core 中的 Razor 页面和 EF Core - 数据模型 - 第 5 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="99e0d-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="99e0d-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="99e0d-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="99e0d-105">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="99e0d-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="99e0d-106">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="99e0d-106">In this tutorial:</span></span>

* <span data-ttu-id="99e0d-107">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="99e0d-108">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="99e0d-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="99e0d-109">已完成数据模型的实体类如下图所示：</span><span class="sxs-lookup"><span data-stu-id="99e0d-109">The entity classes for the completed data model is shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="99e0d-111">如果遇到无法解决的问题，请下载[已完成应用](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-111">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="99e0d-112">使用特性自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="99e0d-112">Customize the data model with attributes</span></span>

<span data-ttu-id="99e0d-113">此部分将使用特性自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="99e0d-113">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="99e0d-114">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-114">The DataType attribute</span></span>

<span data-ttu-id="99e0d-115">学生页面当前显示注册日期。</span><span class="sxs-lookup"><span data-stu-id="99e0d-115">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="99e0d-116">通常情况下，日期字段仅显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="99e0d-116">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="99e0d-117">用以下突出显示的代码更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="99e0d-117">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="99e0d-118">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="99e0d-118">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="99e0d-119">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="99e0d-119">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="99e0d-120">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="99e0d-120">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="99e0d-121">例如:</span><span class="sxs-lookup"><span data-stu-id="99e0d-121">For example:</span></span>

* <span data-ttu-id="99e0d-122">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="99e0d-122">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="99e0d-123">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="99e0d-123">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="99e0d-124">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-124">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="99e0d-125">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="99e0d-125">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="99e0d-126">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="99e0d-126">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="99e0d-127">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="99e0d-127">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="99e0d-128">`DisplayFormat` 特性用于显式指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="99e0d-128">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="99e0d-129">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="99e0d-129">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="99e0d-130">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-130">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="99e0d-131">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="99e0d-131">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="99e0d-132">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-132">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="99e0d-133">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="99e0d-133">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="99e0d-134">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="99e0d-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="99e0d-135">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="99e0d-135">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="99e0d-136">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="99e0d-136">The browser can enable HTML5 features.</span></span> <span data-ttu-id="99e0d-137">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接、客户端输入验证等。</span><span class="sxs-lookup"><span data-stu-id="99e0d-137">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="99e0d-138">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="99e0d-138">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="99e0d-139">有关详细信息，请参阅 [\<input> 标记帮助器文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-139">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="99e0d-140">运行应用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-140">Run the app.</span></span> <span data-ttu-id="99e0d-141">导航到学生索引页。</span><span class="sxs-lookup"><span data-stu-id="99e0d-141">Navigate to the Students Index page.</span></span> <span data-ttu-id="99e0d-142">将不再显示时间。</span><span class="sxs-lookup"><span data-stu-id="99e0d-142">Times are no longer displayed.</span></span> <span data-ttu-id="99e0d-143">使用 `Student` 模型的每个视图将显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="99e0d-143">Every view that uses the `Student` model displays the date without time.</span></span>

![“学生”索引页显示不带时间的日期](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="99e0d-145">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-145">The StringLength attribute</span></span>

<span data-ttu-id="99e0d-146">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="99e0d-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="99e0d-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="99e0d-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="99e0d-148">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="99e0d-148">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="99e0d-149">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="99e0d-149">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="99e0d-150">使用以下代码更新 `Student` 模型：</span><span class="sxs-lookup"><span data-stu-id="99e0d-150">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="99e0d-151">上面的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="99e0d-151">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="99e0d-152">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="99e0d-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="99e0d-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 特性用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="99e0d-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="99e0d-154">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="99e0d-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="99e0d-155">运行应用：</span><span class="sxs-lookup"><span data-stu-id="99e0d-155">Run the app:</span></span>

* <span data-ttu-id="99e0d-156">导航到学生页。</span><span class="sxs-lookup"><span data-stu-id="99e0d-156">Navigate to the Students page.</span></span>
* <span data-ttu-id="99e0d-157">选择“新建”并输入不超过 50 个字符的名称。</span><span class="sxs-lookup"><span data-stu-id="99e0d-157">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="99e0d-158">选择“创建”时，客户端验证会显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="99e0d-158">Select **Create**, client-side validation shows an error message.</span></span>

![显示字符串长度错误的“学生索引”页](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="99e0d-160">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器。</span><span class="sxs-lookup"><span data-stu-id="99e0d-160">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="99e0d-162">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="99e0d-162">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="99e0d-163">名称字段的类型为 `nvarchar(MAX)`，因为数据库上尚未运行迁移。</span><span class="sxs-lookup"><span data-stu-id="99e0d-163">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="99e0d-164">稍后在本教程中运行迁移时，名称字段将变成 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-164">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="99e0d-165">Column 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-165">The Column attribute</span></span>

<span data-ttu-id="99e0d-166">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="99e0d-166">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="99e0d-167">在本部分，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="99e0d-167">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="99e0d-168">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-168">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="99e0d-169">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="99e0d-169">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="99e0d-170">用以下突出显示的代码更新 *Student.cs* 文件：</span><span class="sxs-lookup"><span data-stu-id="99e0d-170">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="99e0d-171">进行上述更改后，应用中的 `Student.FirstMidName` 将映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="99e0d-171">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="99e0d-172">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="99e0d-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="99e0d-173">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="99e0d-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="99e0d-174">如果在执行迁移前运行应用，则会生成如下异常：</span><span class="sxs-lookup"><span data-stu-id="99e0d-174">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="99e0d-175">若要更新数据库：</span><span class="sxs-lookup"><span data-stu-id="99e0d-175">To update the DB:</span></span>

* <span data-ttu-id="99e0d-176">生成项目。</span><span class="sxs-lookup"><span data-stu-id="99e0d-176">Build the project.</span></span>
* <span data-ttu-id="99e0d-177">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="99e0d-177">Open a command window in the project folder.</span></span> <span data-ttu-id="99e0d-178">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="99e0d-178">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99e0d-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99e0d-179">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="99e0d-180">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99e0d-180">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

------

<span data-ttu-id="99e0d-181">`migrations add ColumnFirstName` 命令将生成如下警告消息：</span><span class="sxs-lookup"><span data-stu-id="99e0d-181">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="99e0d-182">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="99e0d-182">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="99e0d-183">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="99e0d-183">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="99e0d-184">测试应用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-184">Test the app.</span></span>

<span data-ttu-id="99e0d-185">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="99e0d-185">Open the Student table in SSOX:</span></span>

![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="99e0d-187">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-187">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="99e0d-188">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-188">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="99e0d-189">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-189">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="99e0d-190">在下一部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="99e0d-190">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="99e0d-191">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="99e0d-191">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="99e0d-192">Student 实体更新</span><span class="sxs-lookup"><span data-stu-id="99e0d-192">Student entity update</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="99e0d-194">用以下代码更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="99e0d-194">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="99e0d-195">Required 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-195">The Required attribute</span></span>

<span data-ttu-id="99e0d-196">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="99e0d-196">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="99e0d-197">值类型（`DateTime`、`int`、`double`）等不可为 NULL 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="99e0d-197">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="99e0d-198">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="99e0d-198">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="99e0d-199">不能用 `StringLength` 特性中的最短长度参数替换 `Required` 特性：</span><span class="sxs-lookup"><span data-stu-id="99e0d-199">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="99e0d-200">Display 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-200">The Display attribute</span></span>

<span data-ttu-id="99e0d-201">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="99e0d-201">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="99e0d-202">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="99e0d-202">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="99e0d-203">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="99e0d-203">The FullName calculated property</span></span>

<span data-ttu-id="99e0d-204">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="99e0d-204">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="99e0d-205">`FullName` 不能设置并且仅具有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="99e0d-205">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="99e0d-206">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="99e0d-206">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="99e0d-207">创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="99e0d-207">Create the Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="99e0d-209">用以下代码创建 Models/Instructor.cs：</span><span class="sxs-lookup"><span data-stu-id="99e0d-209">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="99e0d-210">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="99e0d-210">Multiple attributes can be on one line.</span></span> <span data-ttu-id="99e0d-211">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="99e0d-211">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="99e0d-212">CourseAssignments 和 OfficeAssignment 导航属性</span><span class="sxs-lookup"><span data-stu-id="99e0d-212">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="99e0d-213">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="99e0d-213">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="99e0d-214">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="99e0d-214">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="99e0d-215">如果导航属性包含多个实体：</span><span class="sxs-lookup"><span data-stu-id="99e0d-215">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="99e0d-216">它必须是可在其中添加、删除和更新实体的列表类型。</span><span class="sxs-lookup"><span data-stu-id="99e0d-216">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="99e0d-217">导航属性类型包括：</span><span class="sxs-lookup"><span data-stu-id="99e0d-217">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="99e0d-218">如果指定了 `ICollection<T>`，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="99e0d-218">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="99e0d-219">`CourseAssignment` 实体在“多对多关系”部分进行介绍。</span><span class="sxs-lookup"><span data-stu-id="99e0d-219">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="99e0d-220">Contoso University 业务规则规定一名讲师最多可获得一间办公室。</span><span class="sxs-lookup"><span data-stu-id="99e0d-220">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="99e0d-221">`OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="99e0d-221">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="99e0d-222">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="99e0d-222">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="99e0d-223">创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="99e0d-223">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="99e0d-225">用以下代码创建 Models/OfficeAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="99e0d-225">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="99e0d-226">Key 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-226">The Key attribute</span></span>

<span data-ttu-id="99e0d-227">`[Key]` 特性用于在属性名不是 classnameID 或 ID 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-227">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="99e0d-228">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-228">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="99e0d-229">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="99e0d-229">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="99e0d-230">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-230">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="99e0d-231">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为：</span><span class="sxs-lookup"><span data-stu-id="99e0d-231">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="99e0d-232">`InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="99e0d-232">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="99e0d-233">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="99e0d-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="99e0d-234">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="99e0d-235">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="99e0d-235">The Instructor navigation property</span></span>

<span data-ttu-id="99e0d-236">`Instructor` 实体的 `OfficeAssignment` 导航属性可以为 NULL，因为：</span><span class="sxs-lookup"><span data-stu-id="99e0d-236">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="99e0d-237">引用类型（例如，类可以为 NULL）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-237">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="99e0d-238">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="99e0d-238">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="99e0d-239">`OfficeAssignment` 实体具有不可为 NULL 的 `Instructor` 导航属性，因为：</span><span class="sxs-lookup"><span data-stu-id="99e0d-239">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="99e0d-240">`InstructorID` 不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="99e0d-240">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="99e0d-241">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="99e0d-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="99e0d-242">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="99e0d-243">`[Required]` 特性可以应用于 `Instructor` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="99e0d-243">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="99e0d-244">上面的代码指定必须存在相关的讲师。</span><span class="sxs-lookup"><span data-stu-id="99e0d-244">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="99e0d-245">上面的代码没有必要，因为 `InstructorID` 外键（也是 PK）不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="99e0d-245">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="99e0d-246">修改 Course 实体</span><span class="sxs-lookup"><span data-stu-id="99e0d-246">Modify the Course Entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="99e0d-248">用以下代码更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="99e0d-248">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="99e0d-249">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-249">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="99e0d-250">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="99e0d-250">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="99e0d-251">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="99e0d-251">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="99e0d-252">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="99e0d-252">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="99e0d-253">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="99e0d-253">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="99e0d-254">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-254">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="99e0d-255">数据模型中包含 FK 后可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="99e0d-255">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="99e0d-256">例如，假设某个模型中不包含 FK 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-256">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="99e0d-257">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="99e0d-257">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="99e0d-258">如果未显式加载 `Department` 实体，则该实体将为 NULL。</span><span class="sxs-lookup"><span data-stu-id="99e0d-258">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="99e0d-259">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="99e0d-259">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="99e0d-260">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="99e0d-260">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="99e0d-261">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-261">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="99e0d-262">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="99e0d-262">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="99e0d-263">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="99e0d-263">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="99e0d-264">由数据库生成 PK 值通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="99e0d-264">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="99e0d-265">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="99e0d-265">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="99e0d-266">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="99e0d-266">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="99e0d-267">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="99e0d-267">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="99e0d-268">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="99e0d-268">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="99e0d-269">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-269">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="99e0d-270">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="99e0d-270">Foreign key and navigation properties</span></span>

<span data-ttu-id="99e0d-271">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="99e0d-271">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="99e0d-272">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="99e0d-272">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="99e0d-273">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="99e0d-273">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="99e0d-274">一门课程可能由多位讲师讲授，因此 `CourseAssignments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="99e0d-274">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="99e0d-275">`CourseAssignment` 在[后文](#many-to-many-relationships)介绍。</span><span class="sxs-lookup"><span data-stu-id="99e0d-275">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="99e0d-276">创建 Department 实体</span><span class="sxs-lookup"><span data-stu-id="99e0d-276">Create the Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="99e0d-278">用以下代码创建 Models/Department.cs：</span><span class="sxs-lookup"><span data-stu-id="99e0d-278">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="99e0d-279">Column 特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-279">The Column attribute</span></span>

<span data-ttu-id="99e0d-280">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="99e0d-280">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="99e0d-281">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="99e0d-281">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="99e0d-282">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="99e0d-282">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="99e0d-283">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="99e0d-283">Column mapping is generally not required.</span></span> <span data-ttu-id="99e0d-284">EF Core 通常基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="99e0d-284">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="99e0d-285">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="99e0d-285">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="99e0d-286">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="99e0d-286">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="99e0d-287">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="99e0d-287">Foreign key and navigation properties</span></span>

<span data-ttu-id="99e0d-288">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="99e0d-288">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="99e0d-289">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="99e0d-289">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="99e0d-290">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="99e0d-290">An administrator is always an instructor.</span></span> <span data-ttu-id="99e0d-291">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="99e0d-291">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="99e0d-292">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="99e0d-292">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="99e0d-293">上面代码中的问号 (?) 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="99e0d-293">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="99e0d-294">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="99e0d-294">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="99e0d-295">注意:按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="99e0d-295">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="99e0d-296">级联删除可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="99e0d-296">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="99e0d-297">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="99e0d-297">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="99e0d-298">例如，如果未将 `Department.InstructorID` 属性定义为可以为 NULL：</span><span class="sxs-lookup"><span data-stu-id="99e0d-298">For example, if the `Department.InstructorID` property wasn't defined as nullable:</span></span>

* <span data-ttu-id="99e0d-299">EF Core 会配置将在删除系时删除讲师的级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="99e0d-299">EF Core configures a cascade delete rule to delete the instructor when the department is deleted.</span></span>
* <span data-ttu-id="99e0d-300">在删除系时删除讲师并不是预期行为。</span><span class="sxs-lookup"><span data-stu-id="99e0d-300">Deleting the instructor when the department is deleted isn't the intended behavior.</span></span>

<span data-ttu-id="99e0d-301">如果业务规则要求 `InstructorID` 属性不可为 NULL，请使用以下 Fluent API 语句：</span><span class="sxs-lookup"><span data-stu-id="99e0d-301">If business rules required the `InstructorID` property be non-nullable, use the following fluent API statement:</span></span>

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

<span data-ttu-id="99e0d-302">上面的代码会针对“系-讲师”关系禁用级联删除。</span><span class="sxs-lookup"><span data-stu-id="99e0d-302">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="99e0d-303">更新 Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="99e0d-303">Update the Enrollment entity</span></span>

<span data-ttu-id="99e0d-304">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="99e0d-304">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="99e0d-306">用以下代码更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="99e0d-306">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="99e0d-307">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="99e0d-307">Foreign key and navigation properties</span></span>

<span data-ttu-id="99e0d-308">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="99e0d-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="99e0d-309">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="99e0d-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="99e0d-310">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="99e0d-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="99e0d-311">多对多关系</span><span class="sxs-lookup"><span data-stu-id="99e0d-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="99e0d-312">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="99e0d-313">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表。</span><span class="sxs-lookup"><span data-stu-id="99e0d-313">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="99e0d-314">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据（本教程中为 PK 和 `Grade`）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-314">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="99e0d-315">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="99e0d-315">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="99e0d-316">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="99e0d-316">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="99e0d-317">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="99e0d-317">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="99e0d-319">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-319">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="99e0d-320">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-320">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="99e0d-321">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-321">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="99e0d-322">`Instructor` 和 `Course` 实体具有使用纯联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-322">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="99e0d-323">注意:EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="99e0d-323">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="99e0d-324">有关详细信息，请参阅 [EF Core 2.0 中的多对多关系](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-324">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="99e0d-325">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="99e0d-325">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="99e0d-327">用以下代码创建 Models/CourseAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="99e0d-327">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="99e0d-328">讲师-课程</span><span class="sxs-lookup"><span data-stu-id="99e0d-328">Instructor-to-Courses</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="99e0d-330">讲师-课程的多对多关系：</span><span class="sxs-lookup"><span data-stu-id="99e0d-330">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="99e0d-331">要求必须用实体集表示联接表。</span><span class="sxs-lookup"><span data-stu-id="99e0d-331">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="99e0d-332">为纯联接表（无有效负载的表）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-332">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="99e0d-333">常规做法是将联接实体命名为 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-333">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="99e0d-334">例如，使用此模式的“讲师-课程”联接表是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-334">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="99e0d-335">但是，我们建议使用可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="99e0d-335">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="99e0d-336">数据模型开始时很简单，其内容会逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="99e0d-336">Data models start out simple and grow.</span></span> <span data-ttu-id="99e0d-337">无有效负载联接 (PJT) 通常会发展为包含有效负载。</span><span class="sxs-lookup"><span data-stu-id="99e0d-337">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="99e0d-338">该名称以描述性实体名称开始，因此不需要随联接表更改而更改。</span><span class="sxs-lookup"><span data-stu-id="99e0d-338">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="99e0d-339">理想情况下，联接实体在业务领域中可能具有专业名称（可能是一个词）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-339">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="99e0d-340">例如，可以使用名为“阅读率”的联接实体链接“书籍”和“读客”。</span><span class="sxs-lookup"><span data-stu-id="99e0d-340">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="99e0d-341">对于“讲师-课程”的多对多关系，使用 `CourseAssignment` 比使用`CourseInstructor`更合适。</span><span class="sxs-lookup"><span data-stu-id="99e0d-341">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="99e0d-342">组合键</span><span class="sxs-lookup"><span data-stu-id="99e0d-342">Composite key</span></span>

<span data-ttu-id="99e0d-343">FK 不能为 NULL。</span><span class="sxs-lookup"><span data-stu-id="99e0d-343">FKs are not nullable.</span></span> <span data-ttu-id="99e0d-344">`CourseAssignment` 中的两个 FK（`InstructorID` 和 `CourseID`）共同唯一标识 `CourseAssignment` 表的每一行。</span><span class="sxs-lookup"><span data-stu-id="99e0d-344">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="99e0d-345">`CourseAssignment` 不需要专用的 PK。</span><span class="sxs-lookup"><span data-stu-id="99e0d-345">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="99e0d-346">`InstructorID` 和 `CourseID` 属性充当组合 PK。</span><span class="sxs-lookup"><span data-stu-id="99e0d-346">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="99e0d-347">使用 Fluent API 是向 EF Core 指定组合 PK 的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="99e0d-347">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="99e0d-348">下一部分演示如何配置组合 PK。</span><span class="sxs-lookup"><span data-stu-id="99e0d-348">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="99e0d-349">组合键可确保：</span><span class="sxs-lookup"><span data-stu-id="99e0d-349">The composite key ensures:</span></span>

* <span data-ttu-id="99e0d-350">允许一门课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="99e0d-350">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="99e0d-351">允许一名讲师对应多行。</span><span class="sxs-lookup"><span data-stu-id="99e0d-351">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="99e0d-352">不允许相同的讲师和课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="99e0d-352">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="99e0d-353">`Enrollment` 联接实体定义其自己的 PK，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="99e0d-353">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="99e0d-354">若要防止此类重复：</span><span class="sxs-lookup"><span data-stu-id="99e0d-354">To prevent such duplicates:</span></span>

* <span data-ttu-id="99e0d-355">请在 FK 字段上添加唯一索引，或</span><span class="sxs-lookup"><span data-stu-id="99e0d-355">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="99e0d-356">配置具有主要组合键（与 `CourseAssignment` 类似）的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-356">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="99e0d-357">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-357">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="99e0d-358">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="99e0d-358">Update the DB context</span></span>

<span data-ttu-id="99e0d-359">将以下突出显示的代码添加到 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="99e0d-359">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="99e0d-360">上面的代码添加新实体并配置 `CourseAssignment` 实体的组合 PK。</span><span class="sxs-lookup"><span data-stu-id="99e0d-360">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="99e0d-361">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="99e0d-361">Fluent API alternative to attributes</span></span>

<span data-ttu-id="99e0d-362">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为。</span><span class="sxs-lookup"><span data-stu-id="99e0d-362">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="99e0d-363">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-363">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="99e0d-364">[下面的代码](/ef/core/modeling/#methods-of-configuration)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="99e0d-364">The [following code](/ef/core/modeling/#methods-of-configuration) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="99e0d-365">在本教程中，Fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="99e0d-365">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="99e0d-366">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="99e0d-366">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="99e0d-367">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-367">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="99e0d-368">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="99e0d-368">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="99e0d-369">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="99e0d-369">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="99e0d-370">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="99e0d-370">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="99e0d-371">某些配置只能通过 Fluent API 完成（指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-371">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="99e0d-372">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-372">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="99e0d-373">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="99e0d-373">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="99e0d-374">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="99e0d-374">Choose one of these two approaches.</span></span>
* <span data-ttu-id="99e0d-375">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="99e0d-375">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="99e0d-376">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="99e0d-376">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="99e0d-377">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-377">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="99e0d-378">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-378">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="99e0d-379">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-379">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="99e0d-380">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/#methods-of-configuration)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-380">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="99e0d-381">显示关系的实体关系图</span><span class="sxs-lookup"><span data-stu-id="99e0d-381">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="99e0d-382">下图显示 EF Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="99e0d-382">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="99e0d-384">上面的关系图显示：</span><span class="sxs-lookup"><span data-stu-id="99e0d-384">The preceding diagram shows:</span></span>

* <span data-ttu-id="99e0d-385">几条一对多关系线（1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-385">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="99e0d-386">`Instructor` 和 `OfficeAssignment` 实体之间的一对零或一关系线（1 到 0..1）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-386">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="99e0d-387">`Instructor` 和 `Department` 实体之间的零或一到多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="99e0d-387">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="99e0d-388">使用测试数据为数据库设定种子</span><span class="sxs-lookup"><span data-stu-id="99e0d-388">Seed the DB with Test Data</span></span>

<span data-ttu-id="99e0d-389">更新 Data/DbInitializer.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="99e0d-389">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="99e0d-390">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="99e0d-390">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="99e0d-391">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="99e0d-391">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="99e0d-392">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="99e0d-392">The sample data is used for testing.</span></span> <span data-ttu-id="99e0d-393">有关如何对多对多联接表进行种子设定的示例，请参阅 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="99e0d-393">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="99e0d-394">添加迁移</span><span class="sxs-lookup"><span data-stu-id="99e0d-394">Add a migration</span></span>

<span data-ttu-id="99e0d-395">生成项目。</span><span class="sxs-lookup"><span data-stu-id="99e0d-395">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99e0d-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99e0d-396">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="99e0d-397">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99e0d-397">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

------

<span data-ttu-id="99e0d-398">前面的命令显示可能存在数据丢失的相关警告。</span><span class="sxs-lookup"><span data-stu-id="99e0d-398">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="99e0d-399">如果运行 `database update` 命令，则会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="99e0d-399">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="99e0d-400">应用迁移</span><span class="sxs-lookup"><span data-stu-id="99e0d-400">Apply the migration</span></span>

<span data-ttu-id="99e0d-401">现已有一个数据库，需要考虑如何将未来的更改应用到其中。</span><span class="sxs-lookup"><span data-stu-id="99e0d-401">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="99e0d-402">本教程演示两种方法：</span><span class="sxs-lookup"><span data-stu-id="99e0d-402">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="99e0d-403">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="99e0d-403">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="99e0d-404">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="99e0d-404">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="99e0d-405">虽然此方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="99e0d-405">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="99e0d-406">**说明**：这是本教程的一个可选部分。</span><span class="sxs-lookup"><span data-stu-id="99e0d-406">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="99e0d-407">你可以执行删除和重新创建的相关步骤并跳过此部分。</span><span class="sxs-lookup"><span data-stu-id="99e0d-407">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="99e0d-408">如果希望执行本部分中的步骤，请勿执行删除和重新创建步骤。</span><span class="sxs-lookup"><span data-stu-id="99e0d-408">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="99e0d-409">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="99e0d-409">Drop and re-create the database</span></span>

<span data-ttu-id="99e0d-410">已更新 `DbInitializer` 中的代码将为新实体添加种子数据。</span><span class="sxs-lookup"><span data-stu-id="99e0d-410">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="99e0d-411">若要强制 EF Core 创建新的 DB，请删除并更新 DB：</span><span class="sxs-lookup"><span data-stu-id="99e0d-411">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="99e0d-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99e0d-412">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99e0d-413">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="99e0d-413">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="99e0d-414">从 PMC 运行 `Get-Help about_EntityFrameworkCore`，获取帮助信息。</span><span class="sxs-lookup"><span data-stu-id="99e0d-414">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="99e0d-415">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99e0d-415">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="99e0d-416">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="99e0d-416">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="99e0d-417">项目文件夹包含 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="99e0d-417">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="99e0d-418">在命令窗口中输入以下内容：</span><span class="sxs-lookup"><span data-stu-id="99e0d-418">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

------

<span data-ttu-id="99e0d-419">运行应用。</span><span class="sxs-lookup"><span data-stu-id="99e0d-419">Run the app.</span></span> <span data-ttu-id="99e0d-420">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="99e0d-420">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="99e0d-421">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="99e0d-421">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="99e0d-422">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="99e0d-422">Open the DB in SSOX:</span></span>

* <span data-ttu-id="99e0d-423">如果之前已打开过 SSOX，请单击“刷新”按钮。</span><span class="sxs-lookup"><span data-stu-id="99e0d-423">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="99e0d-424">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="99e0d-424">Expand the **Tables** node.</span></span> <span data-ttu-id="99e0d-425">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="99e0d-425">The created tables are displayed.</span></span>

![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="99e0d-427">查看 CourseAssignment 表：</span><span class="sxs-lookup"><span data-stu-id="99e0d-427">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="99e0d-428">右键单击 CourseAssignment 表，然后选择“查看数据”。</span><span class="sxs-lookup"><span data-stu-id="99e0d-428">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="99e0d-429">验证 CourseAssignment 表包含数据。</span><span class="sxs-lookup"><span data-stu-id="99e0d-429">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="99e0d-431">将迁移应用到现有数据库</span><span class="sxs-lookup"><span data-stu-id="99e0d-431">Apply the migration to the existing database</span></span>

<span data-ttu-id="99e0d-432">本部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="99e0d-432">This section is optional.</span></span> <span data-ttu-id="99e0d-433">只有当跳过之前的[删除并重新创建数据库](#drop)部分时才可以执行上述步骤。</span><span class="sxs-lookup"><span data-stu-id="99e0d-433">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="99e0d-434">当现有数据与迁移一起运行时，可能存在不满足现有数据的 FK 约束。</span><span class="sxs-lookup"><span data-stu-id="99e0d-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="99e0d-435">使用生产数据时，必须采取步骤来迁移现有数据。</span><span class="sxs-lookup"><span data-stu-id="99e0d-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="99e0d-436">本部分提供修复 FK 约束冲突的示例。</span><span class="sxs-lookup"><span data-stu-id="99e0d-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="99e0d-437">务必在备份后执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="99e0d-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="99e0d-438">如果已完成上述部分并更新数据库，则不要执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="99e0d-438">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="99e0d-439">{timestamp}_ComplexDataModel.cs 文件包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="99e0d-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="99e0d-440">上面的代码将向 `Course` 表添加不可为 NULL 的 `DepartmentID` FK。</span><span class="sxs-lookup"><span data-stu-id="99e0d-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="99e0d-441">前面教程中的数据库在 `Course` 中包含行，以便迁移时不会更新表。</span><span class="sxs-lookup"><span data-stu-id="99e0d-441">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="99e0d-442">若要使 `ComplexDataModel` 迁移可与现有数据搭配运行：</span><span class="sxs-lookup"><span data-stu-id="99e0d-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="99e0d-443">请更改代码以便为新列 (`DepartmentID`) 赋予默认值。</span><span class="sxs-lookup"><span data-stu-id="99e0d-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="99e0d-444">创建名为“临时”的虚拟系来充当默认的系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="99e0d-445">修复外键约束</span><span class="sxs-lookup"><span data-stu-id="99e0d-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="99e0d-446">更新 `ComplexDataModel` 类 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="99e0d-446">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="99e0d-447">打开 {timestamp}_ComplexDataModel.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="99e0d-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="99e0d-448">对将 `DepartmentID` 列添加到 `Course` 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="99e0d-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="99e0d-449">添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="99e0d-449">Add the following highlighted code.</span></span> <span data-ttu-id="99e0d-450">新代码在 `.CreateTable( name: "Department"` 块后：</span><span class="sxs-lookup"><span data-stu-id="99e0d-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="99e0d-451">经过上面的更改，`Course` 行将在 `ComplexDataModel` `Up` 方法运行后与“临时”系建立联系。</span><span class="sxs-lookup"><span data-stu-id="99e0d-451">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="99e0d-452">生产应用可能：</span><span class="sxs-lookup"><span data-stu-id="99e0d-452">A production app would:</span></span>

* <span data-ttu-id="99e0d-453">包含用于将 `Department` 行和相关 `Course` 行添加到新 `Department` 行的代码或脚本。</span><span class="sxs-lookup"><span data-stu-id="99e0d-453">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="99e0d-454">不会使用“临时”系或 `Course.DepartmentID` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="99e0d-454">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="99e0d-455">下一教程将介绍相关数据。</span><span class="sxs-lookup"><span data-stu-id="99e0d-455">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99e0d-456">其他资源</span><span class="sxs-lookup"><span data-stu-id="99e0d-456">Additional resources</span></span>

* [<span data-ttu-id="99e0d-457">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="99e0d-457">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="99e0d-458">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="99e0d-458">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="99e0d-459">[上一页](xref:data/ef-rp/migrations)
> [下一页](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="99e0d-459">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>
