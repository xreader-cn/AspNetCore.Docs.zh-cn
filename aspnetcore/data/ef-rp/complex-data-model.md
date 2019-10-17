---
title: ASP.NET Core 中的 Razor 页面和 EF Core - 数据模型 - 第 5 个教程（共 8 个）
author: rick-anderson
description: 本教程将添加更多实体和关系，并通过指定格式设置、验证和映射规则来自定义数据模型。
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 2461bc398cd237dac04f4eb8832c70290663ff56
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259484"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="406cd-103">ASP.NET Core 中的 Razor 页面和 EF Core - 数据模型 - 第 5 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="406cd-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="406cd-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="406cd-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="406cd-105">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="406cd-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="406cd-106">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="406cd-106">In this tutorial:</span></span>

* <span data-ttu-id="406cd-107">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="406cd-108">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="406cd-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="406cd-109">完成的数据模型如下图所示：</span><span class="sxs-lookup"><span data-stu-id="406cd-109">The completed data model is shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="406cd-111">Student 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-111">The Student entity</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="406cd-113">使用以下代码替换 Models/Student.cs 中的代码  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="406cd-114">前面的代码将添加一个 `FullName` 属性，并将以下特性添加到现有属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="406cd-115">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="406cd-115">The FullName calculated property</span></span>

<span data-ttu-id="406cd-116">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="406cd-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="406cd-117">无法设置 `FullName`，因此它只有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="406cd-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="406cd-118">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="406cd-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="406cd-119">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="406cd-120">对于学生注册日期，目前所有页面都显示时间和日期，但只有日期是相关的。</span><span class="sxs-lookup"><span data-stu-id="406cd-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="406cd-121">使用数据注释特性，可更改一次代码，修复每个页面中数据的显示格式。</span><span class="sxs-lookup"><span data-stu-id="406cd-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="406cd-122">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="406cd-123">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="406cd-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="406cd-124">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="406cd-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="406cd-125">例如:</span><span class="sxs-lookup"><span data-stu-id="406cd-125">For example:</span></span>

* <span data-ttu-id="406cd-126">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="406cd-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="406cd-127">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="406cd-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="406cd-128">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性。</span><span class="sxs-lookup"><span data-stu-id="406cd-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="406cd-129">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="406cd-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="406cd-130">DisplayFormat 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="406cd-131">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="406cd-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="406cd-132">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="406cd-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="406cd-133">`DisplayFormat` 特性用于显式指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="406cd-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="406cd-134">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="406cd-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="406cd-135">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="406cd-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="406cd-136">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="406cd-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="406cd-137">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="406cd-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="406cd-138">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="406cd-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="406cd-139">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="406cd-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="406cd-140">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="406cd-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="406cd-141">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="406cd-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="406cd-142">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接和客户端输入验证。</span><span class="sxs-lookup"><span data-stu-id="406cd-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="406cd-143">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="406cd-144">有关详细信息，请参阅 [\<input> 标记帮助器文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="406cd-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="406cd-145">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="406cd-146">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="406cd-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="406cd-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="406cd-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="406cd-148">显示的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="406cd-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="406cd-149">[稍后](#the-required-attribute)显示一个设置最小字符串长度的示例。</span><span class="sxs-lookup"><span data-stu-id="406cd-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="406cd-150">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="406cd-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="406cd-151">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="406cd-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="406cd-152">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="406cd-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="406cd-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 特性可用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="406cd-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="406cd-154">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="406cd-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="406cd-156">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器   。</span><span class="sxs-lookup"><span data-stu-id="406cd-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="406cd-158">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="406cd-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="406cd-159">名称字段具有类型 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="406cd-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="406cd-160">在本教程的后续部分创建和应用迁移时，因为字符串的长度特性，名称字段将变为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="406cd-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="406cd-162">在 SQLite 工具中检查 `Student` 表的列定义。</span><span class="sxs-lookup"><span data-stu-id="406cd-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="406cd-163">名称字段具有类型 `Text`。</span><span class="sxs-lookup"><span data-stu-id="406cd-163">The name fields have type `Text`.</span></span> <span data-ttu-id="406cd-164">请注意，首个名称字段名为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="406cd-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="406cd-165">下一节会将该列的名称更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="406cd-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="406cd-166">Column 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="406cd-167">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="406cd-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="406cd-168">在 `Student` 模型中，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="406cd-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="406cd-169">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="406cd-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="406cd-170">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="406cd-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="406cd-171">使用 `[Column]` 特性，数据模型中的 `Student.FirstMidName` 可映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="406cd-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="406cd-172">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="406cd-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="406cd-173">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="406cd-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="406cd-174">这种差异将通过在本教程后面添加迁移来解决。</span><span class="sxs-lookup"><span data-stu-id="406cd-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="406cd-175">Required 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="406cd-176">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="406cd-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="406cd-177">值类型（`DateTime`、`int` 和 `double`）等不可为 null 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="406cd-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="406cd-178">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="406cd-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="406cd-179">`Required` 特性必须与 `MinimumLength` 结合使用才能强制执行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="406cd-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="406cd-180">`MinimumLength` 和 `Required` 允许通过空格来满足验证。</span><span class="sxs-lookup"><span data-stu-id="406cd-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="406cd-181">使用 `RegularExpression` 特性来完全控制字符串。</span><span class="sxs-lookup"><span data-stu-id="406cd-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="406cd-182">Display 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="406cd-183">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="406cd-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="406cd-184">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="406cd-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="406cd-185">创建迁移</span><span class="sxs-lookup"><span data-stu-id="406cd-185">Create a migration</span></span>

<span data-ttu-id="406cd-186">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="406cd-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="406cd-187">此时引发异常。</span><span class="sxs-lookup"><span data-stu-id="406cd-187">An exception is thrown.</span></span> <span data-ttu-id="406cd-188">`[Column]` 特性导致 EF 希望查找名为 `FirstName` 的列，但数据库中的列名称仍为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="406cd-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="406cd-190">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="406cd-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="406cd-191">在 PMC 中，输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="406cd-192">其中的第一个命令将生成以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="406cd-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="406cd-193">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="406cd-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="406cd-194">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="406cd-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="406cd-195">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="406cd-195">Open the Student table in SSOX:</span></span>

  ![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="406cd-197">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="406cd-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="406cd-198">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="406cd-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="406cd-199">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="406cd-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="406cd-201">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="406cd-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="406cd-202">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="406cd-202">Open a command window in the project folder.</span></span> <span data-ttu-id="406cd-203">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="406cd-204">数据库更新命令显示类似于以下示例的错误：</span><span class="sxs-lookup"><span data-stu-id="406cd-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="406cd-205">在本教程中，解决此错误的方法是删除并重新创建初始迁移。</span><span class="sxs-lookup"><span data-stu-id="406cd-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="406cd-206">有关详细信息，请参阅[迁移教程](xref:data/ef-rp/migrations)顶部的 SQLite 警告说明。</span><span class="sxs-lookup"><span data-stu-id="406cd-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="406cd-207">删除“Migrations”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="406cd-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="406cd-208">运行以下命令以删除数据库，创建新的初始迁移，并应用该迁移：</span><span class="sxs-lookup"><span data-stu-id="406cd-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="406cd-209">在 SQLite 工具中检查 Student 表。</span><span class="sxs-lookup"><span data-stu-id="406cd-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="406cd-210">以前为 FirstMidName 的列现在为 FirstName。</span><span class="sxs-lookup"><span data-stu-id="406cd-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="406cd-211">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="406cd-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="406cd-212">请注意，时间和日期既未输入，也未显示。</span><span class="sxs-lookup"><span data-stu-id="406cd-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="406cd-213">选择“新建”，尝试输入不超过 50 个字符的名称  。</span><span class="sxs-lookup"><span data-stu-id="406cd-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="406cd-214">在以下部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="406cd-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="406cd-215">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="406cd-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="406cd-216">Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-216">The Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="406cd-218">用以下代码创建 Models/Instructor.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="406cd-219">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="406cd-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="406cd-220">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="406cd-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="406cd-221">导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-221">Navigation properties</span></span>

<span data-ttu-id="406cd-222">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="406cd-223">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="406cd-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="406cd-224">讲师最多可以有一个办公室，因此 `OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="406cd-225">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="406cd-226">OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-226">The OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="406cd-228">用以下代码创建 Models/OfficeAssignment.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="406cd-229">Key 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-229">The Key attribute</span></span>

<span data-ttu-id="406cd-230">`[Key]` 特性用于在属性名不是 classnameID 或 ID 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="406cd-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="406cd-231">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="406cd-232">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="406cd-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="406cd-233">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="406cd-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="406cd-234">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为 `InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="406cd-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="406cd-235">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="406cd-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="406cd-236">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="406cd-237">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-237">The Instructor navigation property</span></span>

<span data-ttu-id="406cd-238">`Instructor.OfficeAssignment` 导航属性可以为 null，因为给定的讲师可能没有 `OfficeAssignment` 行。</span><span class="sxs-lookup"><span data-stu-id="406cd-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="406cd-239">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="406cd-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="406cd-240">`OfficeAssignment.Instructor` 导航属性将始终具有一个 Instructor 实体，因为外键 `InstructorID` 类型为 `int`，不可为 null 的值类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="406cd-241">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="406cd-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="406cd-242">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="406cd-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="406cd-243">Course 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-243">The Course Entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="406cd-245">用以下代码更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="406cd-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="406cd-246">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="406cd-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="406cd-247">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="406cd-248">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="406cd-249">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有外键属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="406cd-250">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="406cd-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="406cd-251">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="406cd-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="406cd-252">然而，在数据模型中显式包含 FK 可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="406cd-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="406cd-253">例如，假设某个模型中不包含 FK 属性 `DepartmentID`  。</span><span class="sxs-lookup"><span data-stu-id="406cd-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="406cd-254">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="406cd-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="406cd-255">如果未显式加载，则 `Department` 属性为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="406cd-256">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="406cd-257">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="406cd-258">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="406cd-259">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="406cd-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="406cd-260">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="406cd-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="406cd-261">数据库生成通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="406cd-262">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="406cd-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="406cd-263">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="406cd-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="406cd-264">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="406cd-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="406cd-265">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="406cd-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="406cd-266">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="406cd-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="406cd-267">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="406cd-268">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="406cd-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="406cd-269">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="406cd-270">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="406cd-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="406cd-271">一门课程可能由多位讲师讲授，因此 `CourseAssignments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="406cd-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="406cd-272">`CourseAssignment` 在[后文](#many-to-many-relationships)介绍。</span><span class="sxs-lookup"><span data-stu-id="406cd-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="406cd-273">Department 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-273">The Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="406cd-275">用以下代码创建 Models/Department.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="406cd-276">Column 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-276">The Column attribute</span></span>

<span data-ttu-id="406cd-277">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="406cd-278">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="406cd-279">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="406cd-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="406cd-280">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-280">Column mapping is generally not required.</span></span> <span data-ttu-id="406cd-281">EF Core 基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="406cd-282">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="406cd-283">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="406cd-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="406cd-284">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="406cd-285">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="406cd-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="406cd-286">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="406cd-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="406cd-287">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="406cd-287">An administrator is always an instructor.</span></span> <span data-ttu-id="406cd-288">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="406cd-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="406cd-289">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="406cd-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="406cd-290">上面代码中的问号 (?) 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="406cd-291">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="406cd-292">按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="406cd-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="406cd-293">此默认行为可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="406cd-294">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="406cd-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="406cd-295">例如，如果将 `Department.InstructorID` 属性定义为不可为 null，EF Core 将配置级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="406cd-296">在这种情况下，指定为管理员的讲师被删除时，该学院将被删除。</span><span class="sxs-lookup"><span data-stu-id="406cd-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="406cd-297">在这种情况下，限制规则将更有意义。</span><span class="sxs-lookup"><span data-stu-id="406cd-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="406cd-298">以下 [fluent API](#fluent-api-alternative-to-attributes) 将设置限制规则并禁用级联规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="406cd-299">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-299">The Enrollment entity</span></span>

<span data-ttu-id="406cd-300">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="406cd-300">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="406cd-302">用以下代码更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="406cd-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="406cd-303">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="406cd-304">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="406cd-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="406cd-305">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="406cd-306">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="406cd-307">多对多关系</span><span class="sxs-lookup"><span data-stu-id="406cd-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="406cd-308">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="406cd-309">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表  。</span><span class="sxs-lookup"><span data-stu-id="406cd-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="406cd-310">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据（本教程中为 PK 和 `Grade`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="406cd-311">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="406cd-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="406cd-312">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="406cd-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="406cd-313">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="406cd-313">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="406cd-315">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="406cd-316">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="406cd-317">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="406cd-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="406cd-318">`Instructor` 和 `Course` 实体具有使用纯联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="406cd-319">注意：EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="406cd-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="406cd-320">有关详细信息，请参阅 [EF Core 2.0 中的多对多关系](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。</span><span class="sxs-lookup"><span data-stu-id="406cd-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="406cd-321">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-321">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="406cd-323">用以下代码创建 Models/CourseAssignment.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="406cd-324">“讲师-课程”的多对多关系要求使用联接表，而该联接表的实体则为 CourseAssignment。</span><span class="sxs-lookup"><span data-stu-id="406cd-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="406cd-326">常规做法是将联接实体命名为 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="406cd-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="406cd-327">例如，使用此模式的“讲师-课程”联接表将是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="406cd-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="406cd-328">但是，我们建议使用可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="406cd-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="406cd-329">数据模型开始时很简单，其内容会逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="406cd-329">Data models start out simple and grow.</span></span> <span data-ttu-id="406cd-330">无有效负载的联接表 (PJT) 通常会发展为包含有效负载。</span><span class="sxs-lookup"><span data-stu-id="406cd-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="406cd-331">该名称以描述性实体名称开始，因此不需要随联接表更改而更改。</span><span class="sxs-lookup"><span data-stu-id="406cd-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="406cd-332">理想情况下，联接实体在业务领域中可能具有专业名称（可能是一个词）。</span><span class="sxs-lookup"><span data-stu-id="406cd-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="406cd-333">例如，可以使用名为“阅读率”的联接实体链接“书籍”和“读客”。</span><span class="sxs-lookup"><span data-stu-id="406cd-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="406cd-334">对于“讲师-课程”的多对多关系，使用 `CourseAssignment` 比使用`CourseInstructor`更合适。</span><span class="sxs-lookup"><span data-stu-id="406cd-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="406cd-335">组合键</span><span class="sxs-lookup"><span data-stu-id="406cd-335">Composite key</span></span>

<span data-ttu-id="406cd-336">`CourseAssignment` 中的两个 FK（`InstructorID` 和 `CourseID`）共同唯一标识 `CourseAssignment` 表的每一行。</span><span class="sxs-lookup"><span data-stu-id="406cd-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="406cd-337">`CourseAssignment` 不需要专用的 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="406cd-338">`InstructorID` 和 `CourseID` 属性充当组合 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="406cd-339">使用 Fluent API 是向 EF Core 指定组合 PK 的唯一方法  。</span><span class="sxs-lookup"><span data-stu-id="406cd-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="406cd-340">下一部分演示如何配置组合 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="406cd-341">组合键可确保：</span><span class="sxs-lookup"><span data-stu-id="406cd-341">The composite key ensures that:</span></span>

* <span data-ttu-id="406cd-342">允许一门课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="406cd-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="406cd-343">允许一名讲师对应多行。</span><span class="sxs-lookup"><span data-stu-id="406cd-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="406cd-344">不允许同一讲师和课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="406cd-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="406cd-345">`Enrollment` 联接实体定义其自己的 PK，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="406cd-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="406cd-346">若要防止此类重复：</span><span class="sxs-lookup"><span data-stu-id="406cd-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="406cd-347">请在 FK 字段上添加唯一索引，或</span><span class="sxs-lookup"><span data-stu-id="406cd-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="406cd-348">配置具有主要组合键（与 `CourseAssignment` 类似）的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="406cd-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="406cd-349">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="406cd-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="406cd-350">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="406cd-350">Update the database context</span></span>

<span data-ttu-id="406cd-351">使用以下代码更新 Data/SchoolContext.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="406cd-352">上面的代码添加新实体并配置 `CourseAssignment` 实体的组合 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="406cd-353">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="406cd-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="406cd-354">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为  。</span><span class="sxs-lookup"><span data-stu-id="406cd-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="406cd-355">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="406cd-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="406cd-356">[下面的代码](/ef/core/modeling/#use-fluent-api-to-configure-a-model)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="406cd-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="406cd-357">在本教程中，fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="406cd-358">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="406cd-359">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="406cd-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="406cd-360">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="406cd-361">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="406cd-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="406cd-362">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="406cd-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="406cd-363">某些配置只能通过 Fluent API 完成（指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="406cd-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="406cd-364">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="406cd-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="406cd-365">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="406cd-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="406cd-366">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="406cd-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="406cd-367">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="406cd-368">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="406cd-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="406cd-369">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="406cd-370">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="406cd-371">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="406cd-372">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="406cd-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="406cd-373">实体关系图</span><span class="sxs-lookup"><span data-stu-id="406cd-373">Entity diagram</span></span>

<span data-ttu-id="406cd-374">下图显示 EF Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="406cd-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="406cd-376">上面的关系图显示：</span><span class="sxs-lookup"><span data-stu-id="406cd-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="406cd-377">几条一对多关系线（1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="406cd-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="406cd-378">`Instructor` 和 `OfficeAssignment` 实体之间的一对零或一关系线（1 到 0..1）。</span><span class="sxs-lookup"><span data-stu-id="406cd-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="406cd-379">`Instructor` 和 `Department` 实体之间的零或一到多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="406cd-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="406cd-380">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="406cd-380">Seed the database</span></span>

<span data-ttu-id="406cd-381">更新 Data/DbInitializer.cs 中的代码  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="406cd-382">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="406cd-383">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="406cd-384">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="406cd-384">The sample data is used for testing.</span></span> <span data-ttu-id="406cd-385">有关如何对多对多联接表进行种子设定的示例，请参阅 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="406cd-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="406cd-386">添加迁移</span><span class="sxs-lookup"><span data-stu-id="406cd-386">Add a migration</span></span>

<span data-ttu-id="406cd-387">生成项目。</span><span class="sxs-lookup"><span data-stu-id="406cd-387">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="406cd-389">在 PMC 中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="406cd-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="406cd-390">前面的命令显示可能存在数据丢失的相关警告。</span><span class="sxs-lookup"><span data-stu-id="406cd-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="406cd-391">如果运行 `database update` 命令，则会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="406cd-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="406cd-392">下一节将介绍如何处理此错误。</span><span class="sxs-lookup"><span data-stu-id="406cd-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="406cd-394">如果添加迁移并运行 `database update` 命令，则将生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="406cd-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="406cd-395">下一节将介绍如何避免此错误。</span><span class="sxs-lookup"><span data-stu-id="406cd-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="406cd-396">应用迁移或删除并重新创建</span><span class="sxs-lookup"><span data-stu-id="406cd-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="406cd-397">现已有一个数据库，需要考虑如何将更改应用到其中。</span><span class="sxs-lookup"><span data-stu-id="406cd-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="406cd-398">本教程演示两种替代方法：</span><span class="sxs-lookup"><span data-stu-id="406cd-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="406cd-399">[删除并重新创建数据库](#drop)。</span><span class="sxs-lookup"><span data-stu-id="406cd-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="406cd-400">如果使用 SQLite，请选择此部分。</span><span class="sxs-lookup"><span data-stu-id="406cd-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="406cd-401">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="406cd-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="406cd-402">本部分的说明仅适用于 SQL Server，不适用于 SQLite  。</span><span class="sxs-lookup"><span data-stu-id="406cd-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="406cd-403">这两个选项都适用于 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="406cd-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="406cd-404">虽然应用迁移方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="406cd-405">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="406cd-405">Drop and re-create the database</span></span>

<span data-ttu-id="406cd-406">如果使用 SQL Server 并且想要在以下部分执行应用迁移方法，[请跳过此部分](#apply-the-migration)。</span><span class="sxs-lookup"><span data-stu-id="406cd-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="406cd-407">若要强制 EF Core 创建新的数据库，请删除并更新该数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="406cd-409">在“包管理器控制台”(PMC) 中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="406cd-410">删除“Migrations”文件夹，然后运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="406cd-412">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="406cd-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="406cd-413">项目文件夹包含 ContosoUniversity.csproj 文件  。</span><span class="sxs-lookup"><span data-stu-id="406cd-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="406cd-414">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="406cd-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="406cd-415">删除“Migrations”文件夹，然后运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="406cd-416">运行应用。</span><span class="sxs-lookup"><span data-stu-id="406cd-416">Run the app.</span></span> <span data-ttu-id="406cd-417">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="406cd-418">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="406cd-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="406cd-420">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="406cd-421">如果之前已打开过 SSOX，请单击“刷新”按钮  。</span><span class="sxs-lookup"><span data-stu-id="406cd-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="406cd-422">展开“表”节点  。</span><span class="sxs-lookup"><span data-stu-id="406cd-422">Expand the **Tables** node.</span></span> <span data-ttu-id="406cd-423">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="406cd-423">The created tables are displayed.</span></span>

  ![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="406cd-425">查看 CourseAssignment 表  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="406cd-426">右键单击 CourseAssignment 表，然后选择“查看数据”   。</span><span class="sxs-lookup"><span data-stu-id="406cd-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="406cd-427">验证 CourseAssignment 表包含数据  。</span><span class="sxs-lookup"><span data-stu-id="406cd-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="406cd-430">使用 SQLite 工具检查数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="406cd-431">新的表和列。</span><span class="sxs-lookup"><span data-stu-id="406cd-431">New tables and columns.</span></span>
* <span data-ttu-id="406cd-432">表中设定种子的数据，例如 CourseAssignment 表  。</span><span class="sxs-lookup"><span data-stu-id="406cd-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="406cd-433">应用迁移</span><span class="sxs-lookup"><span data-stu-id="406cd-433">Apply the migration</span></span>

<span data-ttu-id="406cd-434">本部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="406cd-434">This section is optional.</span></span> <span data-ttu-id="406cd-435">这些步骤仅适用于 SQL Server LocalDB，并且仅当跳过前面的[删除并重新创建数据库](#drop)部分时才适用。</span><span class="sxs-lookup"><span data-stu-id="406cd-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="406cd-436">当现有数据与迁移一起运行时，可能存在不满足现有数据的 FK 约束。</span><span class="sxs-lookup"><span data-stu-id="406cd-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="406cd-437">使用生产数据时，必须采取步骤来迁移现有数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="406cd-438">本部分提供修复 FK 约束冲突的示例。</span><span class="sxs-lookup"><span data-stu-id="406cd-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="406cd-439">务必在备份后执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="406cd-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="406cd-440">如果已完成上述[删除并重新创建数据库](#drop)部分，请不要更改这些代码。</span><span class="sxs-lookup"><span data-stu-id="406cd-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="406cd-441">{timestamp}_ComplexDataModel.cs 文件包含以下代码  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="406cd-442">上面的代码将向 `Course` 表添加不可为 NULL 的 `DepartmentID` FK。</span><span class="sxs-lookup"><span data-stu-id="406cd-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="406cd-443">前面教程中的数据库在 `Course` 中包含行，以便迁移时不会更新表。</span><span class="sxs-lookup"><span data-stu-id="406cd-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="406cd-444">若要使 `ComplexDataModel` 迁移可与现有数据搭配运行：</span><span class="sxs-lookup"><span data-stu-id="406cd-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="406cd-445">请更改代码以便为新列 (`DepartmentID`) 赋予默认值。</span><span class="sxs-lookup"><span data-stu-id="406cd-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="406cd-446">创建名为“临时”的虚拟系来充当默认的系。</span><span class="sxs-lookup"><span data-stu-id="406cd-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="406cd-447">修复外键约束</span><span class="sxs-lookup"><span data-stu-id="406cd-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="406cd-448">在 `ComplexDataModel` 迁移类中，更新 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="406cd-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="406cd-449">打开 {timestamp}_ComplexDataModel.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="406cd-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="406cd-450">对将 `DepartmentID` 列添加到 `Course` 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="406cd-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="406cd-451">添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="406cd-451">Add the following highlighted code.</span></span> <span data-ttu-id="406cd-452">新代码在 `.CreateTable( name: "Department"` 块后：</span><span class="sxs-lookup"><span data-stu-id="406cd-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="406cd-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span><span class="sxs-lookup"><span data-stu-id="406cd-453">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="406cd-454">经过上面的更改，现有的 `Course` 行将在 `ComplexDataModel.Up` 方法运行后与“临时”院系建立联系。</span><span class="sxs-lookup"><span data-stu-id="406cd-454">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="406cd-455">本教程简化了此处所示的处理方式。</span><span class="sxs-lookup"><span data-stu-id="406cd-455">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="406cd-456">生产应用可能：</span><span class="sxs-lookup"><span data-stu-id="406cd-456">A production app would:</span></span>

* <span data-ttu-id="406cd-457">包含用于将 `Department` 行和相关 `Course` 行添加到新 `Department` 行的代码或脚本。</span><span class="sxs-lookup"><span data-stu-id="406cd-457">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="406cd-458">不会使用“临时”系或 `Course.DepartmentID` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="406cd-458">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="406cd-460">在“包管理器控制台”(PMC) 中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-460">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="406cd-461">由于 `DbInitializer.Initialize` 方法仅适用于空数据库，因此请使用 SSOX 删除 Student 表和 Course 表中的所有行。</span><span class="sxs-lookup"><span data-stu-id="406cd-461">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="406cd-462">（级联删除将负责 Enrollment 表。）</span><span class="sxs-lookup"><span data-stu-id="406cd-462">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-463">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-463">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="406cd-464">如果在 Visual Studio Code 中使用 SQL Server LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="406cd-464">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="406cd-465">运行应用。</span><span class="sxs-lookup"><span data-stu-id="406cd-465">Run the app.</span></span> <span data-ttu-id="406cd-466">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-466">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="406cd-467">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="406cd-467">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="406cd-468">后续步骤</span><span class="sxs-lookup"><span data-stu-id="406cd-468">Next steps</span></span>

<span data-ttu-id="406cd-469">接下来的两个教程演示如何读取和更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-469">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="406cd-470">[上一个教程](xref:data/ef-rp/migrations)
> [下一个教程](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="406cd-470">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="406cd-471">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="406cd-471">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="406cd-472">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="406cd-472">In this tutorial:</span></span>

* <span data-ttu-id="406cd-473">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-473">More entities and relationships are added.</span></span>
* <span data-ttu-id="406cd-474">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="406cd-474">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="406cd-475">已完成数据模型的实体类如下图所示：</span><span class="sxs-lookup"><span data-stu-id="406cd-475">The entity classes for the completed data model are shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="406cd-477">如果遇到无法解决的问题，请下载[已完成应用](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="406cd-477">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="406cd-478">使用特性自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="406cd-478">Customize the data model with attributes</span></span>

<span data-ttu-id="406cd-479">此部分将使用特性自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="406cd-479">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="406cd-480">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-480">The DataType attribute</span></span>

<span data-ttu-id="406cd-481">学生页面当前显示注册日期。</span><span class="sxs-lookup"><span data-stu-id="406cd-481">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="406cd-482">通常情况下，日期字段仅显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="406cd-482">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="406cd-483">用以下突出显示的代码更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="406cd-483">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="406cd-484">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-484">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="406cd-485">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="406cd-485">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="406cd-486">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="406cd-486">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="406cd-487">例如:</span><span class="sxs-lookup"><span data-stu-id="406cd-487">For example:</span></span>

* <span data-ttu-id="406cd-488">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="406cd-488">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="406cd-489">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="406cd-489">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="406cd-490">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。</span><span class="sxs-lookup"><span data-stu-id="406cd-490">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="406cd-491">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="406cd-491">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="406cd-492">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="406cd-492">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="406cd-493">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="406cd-493">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="406cd-494">`DisplayFormat` 特性用于显式指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="406cd-494">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="406cd-495">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="406cd-495">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="406cd-496">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="406cd-496">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="406cd-497">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="406cd-497">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="406cd-498">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="406cd-498">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="406cd-499">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="406cd-499">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="406cd-500">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="406cd-500">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="406cd-501">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="406cd-501">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="406cd-502">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="406cd-502">The browser can enable HTML5 features.</span></span> <span data-ttu-id="406cd-503">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接、客户端输入验证等。</span><span class="sxs-lookup"><span data-stu-id="406cd-503">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="406cd-504">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-504">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="406cd-505">有关详细信息，请参阅 [\<input> 标记帮助器文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="406cd-505">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="406cd-506">运行应用。</span><span class="sxs-lookup"><span data-stu-id="406cd-506">Run the app.</span></span> <span data-ttu-id="406cd-507">导航到学生索引页。</span><span class="sxs-lookup"><span data-stu-id="406cd-507">Navigate to the Students Index page.</span></span> <span data-ttu-id="406cd-508">将不再显示时间。</span><span class="sxs-lookup"><span data-stu-id="406cd-508">Times are no longer displayed.</span></span> <span data-ttu-id="406cd-509">使用 `Student` 模型的每个视图将显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="406cd-509">Every view that uses the `Student` model displays the date without time.</span></span>

![“学生”索引页显示不带时间的日期](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="406cd-511">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-511">The StringLength attribute</span></span>

<span data-ttu-id="406cd-512">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="406cd-512">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="406cd-513">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="406cd-513">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="406cd-514">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="406cd-514">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="406cd-515">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="406cd-515">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="406cd-516">使用以下代码更新 `Student` 模型：</span><span class="sxs-lookup"><span data-stu-id="406cd-516">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="406cd-517">上面的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="406cd-517">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="406cd-518">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="406cd-518">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="406cd-519">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) 特性用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="406cd-519">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="406cd-520">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="406cd-520">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="406cd-521">运行应用：</span><span class="sxs-lookup"><span data-stu-id="406cd-521">Run the app:</span></span>

* <span data-ttu-id="406cd-522">导航到学生页。</span><span class="sxs-lookup"><span data-stu-id="406cd-522">Navigate to the Students page.</span></span>
* <span data-ttu-id="406cd-523">选择“新建”并输入不超过 50 个字符的名称  。</span><span class="sxs-lookup"><span data-stu-id="406cd-523">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="406cd-524">选择“创建”时，客户端验证会显示一条错误消息  。</span><span class="sxs-lookup"><span data-stu-id="406cd-524">Select **Create**, client-side validation shows an error message.</span></span>

![显示字符串长度错误的“学生索引”页](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="406cd-526">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器   。</span><span class="sxs-lookup"><span data-stu-id="406cd-526">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="406cd-528">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="406cd-528">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="406cd-529">名称字段的类型为 `nvarchar(MAX)`，因为数据库上尚未运行迁移。</span><span class="sxs-lookup"><span data-stu-id="406cd-529">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="406cd-530">稍后在本教程中运行迁移时，名称字段将变成 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="406cd-530">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="406cd-531">Column 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-531">The Column attribute</span></span>

<span data-ttu-id="406cd-532">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="406cd-532">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="406cd-533">在本部分，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="406cd-533">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="406cd-534">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="406cd-534">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="406cd-535">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="406cd-535">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="406cd-536">用以下突出显示的代码更新 *Student.cs* 文件：</span><span class="sxs-lookup"><span data-stu-id="406cd-536">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="406cd-537">进行上述更改后，应用中的 `Student.FirstMidName` 将映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="406cd-537">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="406cd-538">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="406cd-538">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="406cd-539">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="406cd-539">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="406cd-540">如果在执行迁移前运行应用，则会生成如下异常：</span><span class="sxs-lookup"><span data-stu-id="406cd-540">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="406cd-541">若要更新数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-541">To update the DB:</span></span>

* <span data-ttu-id="406cd-542">生成项目。</span><span class="sxs-lookup"><span data-stu-id="406cd-542">Build the project.</span></span>
* <span data-ttu-id="406cd-543">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="406cd-543">Open a command window in the project folder.</span></span> <span data-ttu-id="406cd-544">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-544">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-545">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-546">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-546">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="406cd-547">`migrations add ColumnFirstName` 命令将生成如下警告消息：</span><span class="sxs-lookup"><span data-stu-id="406cd-547">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="406cd-548">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="406cd-548">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="406cd-549">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="406cd-549">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="406cd-550">测试应用。</span><span class="sxs-lookup"><span data-stu-id="406cd-550">Test the app.</span></span>

<span data-ttu-id="406cd-551">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="406cd-551">Open the Student table in SSOX:</span></span>

![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="406cd-553">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="406cd-553">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="406cd-554">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="406cd-554">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="406cd-555">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="406cd-555">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="406cd-556">在下一部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="406cd-556">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="406cd-557">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="406cd-557">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="406cd-558">Student 实体更新</span><span class="sxs-lookup"><span data-stu-id="406cd-558">Student entity update</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="406cd-560">用以下代码更新 *Models/Student.cs*：</span><span class="sxs-lookup"><span data-stu-id="406cd-560">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="406cd-561">Required 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-561">The Required attribute</span></span>

<span data-ttu-id="406cd-562">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="406cd-562">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="406cd-563">值类型（`DateTime`、`int`、`double`）等不可为 NULL 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="406cd-563">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="406cd-564">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="406cd-564">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="406cd-565">不能用 `StringLength` 特性中的最短长度参数替换 `Required` 特性：</span><span class="sxs-lookup"><span data-stu-id="406cd-565">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="406cd-566">Display 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-566">The Display attribute</span></span>

<span data-ttu-id="406cd-567">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="406cd-567">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="406cd-568">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="406cd-568">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="406cd-569">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="406cd-569">The FullName calculated property</span></span>

<span data-ttu-id="406cd-570">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="406cd-570">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="406cd-571">`FullName` 不能设置并且仅具有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="406cd-571">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="406cd-572">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="406cd-572">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="406cd-573">创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-573">Create the Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="406cd-575">用以下代码创建 Models/Instructor.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-575">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="406cd-576">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="406cd-576">Multiple attributes can be on one line.</span></span> <span data-ttu-id="406cd-577">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="406cd-577">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="406cd-578">CourseAssignments 和 OfficeAssignment 导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-578">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="406cd-579">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-579">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="406cd-580">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="406cd-580">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="406cd-581">如果导航属性包含多个实体：</span><span class="sxs-lookup"><span data-stu-id="406cd-581">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="406cd-582">它必须是可在其中添加、删除和更新实体的列表类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-582">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="406cd-583">导航属性类型包括：</span><span class="sxs-lookup"><span data-stu-id="406cd-583">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="406cd-584">如果指定了 `ICollection<T>`，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="406cd-584">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="406cd-585">`CourseAssignment` 实体在“多对多关系”部分进行介绍。</span><span class="sxs-lookup"><span data-stu-id="406cd-585">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="406cd-586">Contoso University 业务规则规定一名讲师最多可获得一间办公室。</span><span class="sxs-lookup"><span data-stu-id="406cd-586">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="406cd-587">`OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-587">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="406cd-588">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-588">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="406cd-589">创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-589">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="406cd-591">用以下代码创建 Models/OfficeAssignment.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-591">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="406cd-592">Key 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-592">The Key attribute</span></span>

<span data-ttu-id="406cd-593">`[Key]` 特性用于在属性名不是 classnameID 或 ID 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="406cd-593">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="406cd-594">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-594">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="406cd-595">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="406cd-595">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="406cd-596">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="406cd-596">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="406cd-597">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为：</span><span class="sxs-lookup"><span data-stu-id="406cd-597">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="406cd-598">`InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="406cd-598">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="406cd-599">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="406cd-599">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="406cd-600">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-600">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="406cd-601">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-601">The Instructor navigation property</span></span>

<span data-ttu-id="406cd-602">`Instructor` 实体的 `OfficeAssignment` 导航属性可以为 NULL，因为：</span><span class="sxs-lookup"><span data-stu-id="406cd-602">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="406cd-603">引用类型（例如，类可以为 NULL）。</span><span class="sxs-lookup"><span data-stu-id="406cd-603">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="406cd-604">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="406cd-604">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="406cd-605">`OfficeAssignment` 实体具有不可为 NULL 的 `Instructor` 导航属性，因为：</span><span class="sxs-lookup"><span data-stu-id="406cd-605">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="406cd-606">`InstructorID` 不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-606">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="406cd-607">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="406cd-607">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="406cd-608">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="406cd-608">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="406cd-609">`[Required]` 特性可以应用于 `Instructor` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-609">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="406cd-610">上面的代码指定必须存在相关的讲师。</span><span class="sxs-lookup"><span data-stu-id="406cd-610">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="406cd-611">上面的代码没有必要，因为 `InstructorID` 外键（也是 PK）不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-611">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="406cd-612">修改 Course 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-612">Modify the Course Entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="406cd-614">用以下代码更新 *Models/Course.cs*：</span><span class="sxs-lookup"><span data-stu-id="406cd-614">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="406cd-615">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="406cd-615">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="406cd-616">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-616">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="406cd-617">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-617">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="406cd-618">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-618">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="406cd-619">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="406cd-619">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="406cd-620">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="406cd-620">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="406cd-621">数据模型中包含 FK 后可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="406cd-621">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="406cd-622">例如，假设某个模型中不包含 FK 属性 `DepartmentID`  。</span><span class="sxs-lookup"><span data-stu-id="406cd-622">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="406cd-623">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="406cd-623">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="406cd-624">如果未显式加载 `Department` 实体，则该实体将为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-624">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="406cd-625">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-625">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="406cd-626">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="406cd-626">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="406cd-627">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-627">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="406cd-628">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="406cd-628">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="406cd-629">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="406cd-629">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="406cd-630">由数据库生成 PK 值通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-630">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="406cd-631">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="406cd-631">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="406cd-632">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="406cd-632">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="406cd-633">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="406cd-633">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="406cd-634">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="406cd-634">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="406cd-635">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="406cd-635">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="406cd-636">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-636">Foreign key and navigation properties</span></span>

<span data-ttu-id="406cd-637">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="406cd-637">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="406cd-638">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="406cd-638">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="406cd-639">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="406cd-639">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="406cd-640">一门课程可能由多位讲师讲授，因此 `CourseAssignments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="406cd-640">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="406cd-641">`CourseAssignment` 在[后文](#many-to-many-relationships)介绍。</span><span class="sxs-lookup"><span data-stu-id="406cd-641">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="406cd-642">创建 Department 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-642">Create the Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="406cd-644">用以下代码创建 Models/Department.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-644">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="406cd-645">Column 特性</span><span class="sxs-lookup"><span data-stu-id="406cd-645">The Column attribute</span></span>

<span data-ttu-id="406cd-646">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-646">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="406cd-647">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-647">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="406cd-648">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="406cd-648">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="406cd-649">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-649">Column mapping is generally not required.</span></span> <span data-ttu-id="406cd-650">EF Core 通常基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-650">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="406cd-651">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="406cd-651">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="406cd-652">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="406cd-652">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="406cd-653">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-653">Foreign key and navigation properties</span></span>

<span data-ttu-id="406cd-654">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="406cd-654">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="406cd-655">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="406cd-655">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="406cd-656">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="406cd-656">An administrator is always an instructor.</span></span> <span data-ttu-id="406cd-657">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="406cd-657">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="406cd-658">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="406cd-658">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="406cd-659">上面代码中的问号 (?) 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-659">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="406cd-660">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-660">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="406cd-661">注意：按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="406cd-661">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="406cd-662">级联删除可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-662">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="406cd-663">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="406cd-663">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="406cd-664">例如，如果 `Department.InstructorID` 属性定义为不可为 NULL：</span><span class="sxs-lookup"><span data-stu-id="406cd-664">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="406cd-665">EF Core 会配置级联删除规则，以在删除讲师时删除院系。</span><span class="sxs-lookup"><span data-stu-id="406cd-665">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="406cd-666">在删除讲师时删除院系并不是预期行为。</span><span class="sxs-lookup"><span data-stu-id="406cd-666">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="406cd-667">以下 [fluent API](#fluent-api-alternative-to-attributes) 将设置限制规则而不是级联规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-667">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="406cd-668">上面的代码会针对“系-讲师”关系禁用级联删除。</span><span class="sxs-lookup"><span data-stu-id="406cd-668">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="406cd-669">更新 Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-669">Update the Enrollment entity</span></span>

<span data-ttu-id="406cd-670">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="406cd-670">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="406cd-672">用以下代码更新 *Models/Enrollment.cs*：</span><span class="sxs-lookup"><span data-stu-id="406cd-672">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="406cd-673">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="406cd-673">Foreign key and navigation properties</span></span>

<span data-ttu-id="406cd-674">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="406cd-674">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="406cd-675">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-675">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="406cd-676">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="406cd-676">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="406cd-677">多对多关系</span><span class="sxs-lookup"><span data-stu-id="406cd-677">Many-to-Many Relationships</span></span>

<span data-ttu-id="406cd-678">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-678">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="406cd-679">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表  。</span><span class="sxs-lookup"><span data-stu-id="406cd-679">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="406cd-680">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据（本教程中为 PK 和 `Grade`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-680">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="406cd-681">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="406cd-681">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="406cd-682">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="406cd-682">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="406cd-683">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="406cd-683">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="406cd-685">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-685">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="406cd-686">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-686">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="406cd-687">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="406cd-687">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="406cd-688">`Instructor` 和 `Course` 实体具有使用纯联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="406cd-688">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="406cd-689">注意：EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="406cd-689">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="406cd-690">有关详细信息，请参阅 [EF Core 2.0 中的多对多关系](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。</span><span class="sxs-lookup"><span data-stu-id="406cd-690">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="406cd-691">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="406cd-691">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="406cd-693">用以下代码创建 Models/CourseAssignment.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-693">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="406cd-694">讲师-课程</span><span class="sxs-lookup"><span data-stu-id="406cd-694">Instructor-to-Courses</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="406cd-696">讲师-课程的多对多关系：</span><span class="sxs-lookup"><span data-stu-id="406cd-696">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="406cd-697">要求必须用实体集表示联接表。</span><span class="sxs-lookup"><span data-stu-id="406cd-697">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="406cd-698">为纯联接表（无有效负载的表）。</span><span class="sxs-lookup"><span data-stu-id="406cd-698">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="406cd-699">常规做法是将联接实体命名为 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="406cd-699">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="406cd-700">例如，使用此模式的“讲师-课程”联接表是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="406cd-700">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="406cd-701">但是，我们建议使用可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="406cd-701">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="406cd-702">数据模型开始时很简单，其内容会逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="406cd-702">Data models start out simple and grow.</span></span> <span data-ttu-id="406cd-703">无有效负载联接 (PJT) 通常会发展为包含有效负载。</span><span class="sxs-lookup"><span data-stu-id="406cd-703">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="406cd-704">该名称以描述性实体名称开始，因此不需要随联接表更改而更改。</span><span class="sxs-lookup"><span data-stu-id="406cd-704">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="406cd-705">理想情况下，联接实体在业务领域中可能具有专业名称（可能是一个词）。</span><span class="sxs-lookup"><span data-stu-id="406cd-705">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="406cd-706">例如，可以使用名为“阅读率”的联接实体链接“书籍”和“读客”。</span><span class="sxs-lookup"><span data-stu-id="406cd-706">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="406cd-707">对于“讲师-课程”的多对多关系，使用 `CourseAssignment` 比使用`CourseInstructor`更合适。</span><span class="sxs-lookup"><span data-stu-id="406cd-707">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="406cd-708">组合键</span><span class="sxs-lookup"><span data-stu-id="406cd-708">Composite key</span></span>

<span data-ttu-id="406cd-709">FK 不能为 NULL。</span><span class="sxs-lookup"><span data-stu-id="406cd-709">FKs are not nullable.</span></span> <span data-ttu-id="406cd-710">`CourseAssignment` 中的两个 FK（`InstructorID` 和 `CourseID`）共同唯一标识 `CourseAssignment` 表的每一行。</span><span class="sxs-lookup"><span data-stu-id="406cd-710">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="406cd-711">`CourseAssignment` 不需要专用的 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-711">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="406cd-712">`InstructorID` 和 `CourseID` 属性充当组合 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-712">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="406cd-713">使用 Fluent API 是向 EF Core 指定组合 PK 的唯一方法  。</span><span class="sxs-lookup"><span data-stu-id="406cd-713">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="406cd-714">下一部分演示如何配置组合 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-714">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="406cd-715">组合键可确保：</span><span class="sxs-lookup"><span data-stu-id="406cd-715">The composite key ensures:</span></span>

* <span data-ttu-id="406cd-716">允许一门课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="406cd-716">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="406cd-717">允许一名讲师对应多行。</span><span class="sxs-lookup"><span data-stu-id="406cd-717">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="406cd-718">不允许相同的讲师和课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="406cd-718">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="406cd-719">`Enrollment` 联接实体定义其自己的 PK，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="406cd-719">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="406cd-720">若要防止此类重复：</span><span class="sxs-lookup"><span data-stu-id="406cd-720">To prevent such duplicates:</span></span>

* <span data-ttu-id="406cd-721">请在 FK 字段上添加唯一索引，或</span><span class="sxs-lookup"><span data-stu-id="406cd-721">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="406cd-722">配置具有主要组合键（与 `CourseAssignment` 类似）的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="406cd-722">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="406cd-723">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="406cd-723">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="406cd-724">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="406cd-724">Update the DB context</span></span>

<span data-ttu-id="406cd-725">将以下突出显示的代码添加到 Data/SchoolContext.cs  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-725">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="406cd-726">上面的代码添加新实体并配置 `CourseAssignment` 实体的组合 PK。</span><span class="sxs-lookup"><span data-stu-id="406cd-726">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="406cd-727">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="406cd-727">Fluent API alternative to attributes</span></span>

<span data-ttu-id="406cd-728">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为  。</span><span class="sxs-lookup"><span data-stu-id="406cd-728">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="406cd-729">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="406cd-729">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="406cd-730">[下面的代码](/ef/core/modeling/#use-fluent-api-to-configure-a-model)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="406cd-730">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="406cd-731">在本教程中，Fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="406cd-731">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="406cd-732">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-732">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="406cd-733">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="406cd-733">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="406cd-734">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="406cd-734">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="406cd-735">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="406cd-735">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="406cd-736">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="406cd-736">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="406cd-737">某些配置只能通过 Fluent API 完成（指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="406cd-737">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="406cd-738">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="406cd-738">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="406cd-739">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="406cd-739">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="406cd-740">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="406cd-740">Choose one of these two approaches.</span></span>
* <span data-ttu-id="406cd-741">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-741">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="406cd-742">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="406cd-742">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="406cd-743">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-743">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="406cd-744">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-744">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="406cd-745">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="406cd-745">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="406cd-746">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="406cd-746">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="406cd-747">显示关系的实体关系图</span><span class="sxs-lookup"><span data-stu-id="406cd-747">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="406cd-748">下图显示 EF Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="406cd-748">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="406cd-750">上面的关系图显示：</span><span class="sxs-lookup"><span data-stu-id="406cd-750">The preceding diagram shows:</span></span>

* <span data-ttu-id="406cd-751">几条一对多关系线（1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="406cd-751">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="406cd-752">`Instructor` 和 `OfficeAssignment` 实体之间的一对零或一关系线（1 到 0..1）。</span><span class="sxs-lookup"><span data-stu-id="406cd-752">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="406cd-753">`Instructor` 和 `Department` 实体之间的零或一到多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="406cd-753">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="406cd-754">使用测试数据为数据库设定种子</span><span class="sxs-lookup"><span data-stu-id="406cd-754">Seed the DB with Test Data</span></span>

<span data-ttu-id="406cd-755">更新 Data/DbInitializer.cs 中的代码  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-755">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="406cd-756">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-756">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="406cd-757">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-757">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="406cd-758">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="406cd-758">The sample data is used for testing.</span></span> <span data-ttu-id="406cd-759">有关如何对多对多联接表进行种子设定的示例，请参阅 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="406cd-759">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="406cd-760">添加迁移</span><span class="sxs-lookup"><span data-stu-id="406cd-760">Add a migration</span></span>

<span data-ttu-id="406cd-761">生成项目。</span><span class="sxs-lookup"><span data-stu-id="406cd-761">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-762">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-762">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-763">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-763">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="406cd-764">前面的命令显示可能存在数据丢失的相关警告。</span><span class="sxs-lookup"><span data-stu-id="406cd-764">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="406cd-765">如果运行 `database update` 命令，则会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="406cd-765">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="406cd-766">应用迁移</span><span class="sxs-lookup"><span data-stu-id="406cd-766">Apply the migration</span></span>

<span data-ttu-id="406cd-767">现已有一个数据库，需要考虑如何将未来的更改应用到其中。</span><span class="sxs-lookup"><span data-stu-id="406cd-767">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="406cd-768">本教程演示两种方法：</span><span class="sxs-lookup"><span data-stu-id="406cd-768">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="406cd-769">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="406cd-769">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="406cd-770">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="406cd-770">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="406cd-771">虽然此方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-771">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="406cd-772">**说明**：这是本教程的一个可选部分。</span><span class="sxs-lookup"><span data-stu-id="406cd-772">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="406cd-773">你可以执行删除和重新创建的相关步骤并跳过此部分。</span><span class="sxs-lookup"><span data-stu-id="406cd-773">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="406cd-774">如果希望执行本部分中的步骤，请勿执行删除和重新创建步骤。</span><span class="sxs-lookup"><span data-stu-id="406cd-774">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="406cd-775">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="406cd-775">Drop and re-create the database</span></span>

<span data-ttu-id="406cd-776">已更新 `DbInitializer` 中的代码将为新实体添加种子数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-776">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="406cd-777">若要强制 EF Core 创建新的 DB，请删除并更新 DB：</span><span class="sxs-lookup"><span data-stu-id="406cd-777">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="406cd-778">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cd-778">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="406cd-779">在“包管理器控制台”(PMC) 中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-779">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="406cd-780">从 PMC 运行 `Get-Help about_EntityFrameworkCore`，获取帮助信息。</span><span class="sxs-lookup"><span data-stu-id="406cd-780">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="406cd-781">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cd-781">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="406cd-782">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="406cd-782">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="406cd-783">项目文件夹包含 Startup.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="406cd-783">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="406cd-784">在命令窗口中输入以下内容：</span><span class="sxs-lookup"><span data-stu-id="406cd-784">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="406cd-785">运行应用。</span><span class="sxs-lookup"><span data-stu-id="406cd-785">Run the app.</span></span> <span data-ttu-id="406cd-786">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="406cd-786">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="406cd-787">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="406cd-787">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="406cd-788">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="406cd-788">Open the DB in SSOX:</span></span>

* <span data-ttu-id="406cd-789">如果之前已打开过 SSOX，请单击“刷新”按钮  。</span><span class="sxs-lookup"><span data-stu-id="406cd-789">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="406cd-790">展开“表”节点  。</span><span class="sxs-lookup"><span data-stu-id="406cd-790">Expand the **Tables** node.</span></span> <span data-ttu-id="406cd-791">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="406cd-791">The created tables are displayed.</span></span>

![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="406cd-793">查看 CourseAssignment 表  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-793">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="406cd-794">右键单击 CourseAssignment 表，然后选择“查看数据”   。</span><span class="sxs-lookup"><span data-stu-id="406cd-794">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="406cd-795">验证 CourseAssignment 表包含数据  。</span><span class="sxs-lookup"><span data-stu-id="406cd-795">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="406cd-797">将迁移应用到现有数据库</span><span class="sxs-lookup"><span data-stu-id="406cd-797">Apply the migration to the existing database</span></span>

<span data-ttu-id="406cd-798">本部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="406cd-798">This section is optional.</span></span> <span data-ttu-id="406cd-799">只有当跳过之前的[删除并重新创建数据库](#drop)部分时才可以执行上述步骤。</span><span class="sxs-lookup"><span data-stu-id="406cd-799">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="406cd-800">当现有数据与迁移一起运行时，可能存在不满足现有数据的 FK 约束。</span><span class="sxs-lookup"><span data-stu-id="406cd-800">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="406cd-801">使用生产数据时，必须采取步骤来迁移现有数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-801">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="406cd-802">本部分提供修复 FK 约束冲突的示例。</span><span class="sxs-lookup"><span data-stu-id="406cd-802">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="406cd-803">务必在备份后执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="406cd-803">Don't make these code changes without a backup.</span></span> <span data-ttu-id="406cd-804">如果已完成上述部分并更新数据库，则不要执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="406cd-804">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="406cd-805">{timestamp}_ComplexDataModel.cs 文件包含以下代码  ：</span><span class="sxs-lookup"><span data-stu-id="406cd-805">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="406cd-806">上面的代码将向 `Course` 表添加不可为 NULL 的 `DepartmentID` FK。</span><span class="sxs-lookup"><span data-stu-id="406cd-806">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="406cd-807">前面教程中的数据库在 `Course` 中包含行，以便迁移时不会更新表。</span><span class="sxs-lookup"><span data-stu-id="406cd-807">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="406cd-808">若要使 `ComplexDataModel` 迁移可与现有数据搭配运行：</span><span class="sxs-lookup"><span data-stu-id="406cd-808">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="406cd-809">请更改代码以便为新列 (`DepartmentID`) 赋予默认值。</span><span class="sxs-lookup"><span data-stu-id="406cd-809">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="406cd-810">创建名为“临时”的虚拟系来充当默认的系。</span><span class="sxs-lookup"><span data-stu-id="406cd-810">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="406cd-811">修复外键约束</span><span class="sxs-lookup"><span data-stu-id="406cd-811">Fix the foreign key constraints</span></span>

<span data-ttu-id="406cd-812">更新 `ComplexDataModel` 类 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="406cd-812">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="406cd-813">打开 {timestamp}_ComplexDataModel.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="406cd-813">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="406cd-814">对将 `DepartmentID` 列添加到 `Course` 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="406cd-814">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="406cd-815">添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="406cd-815">Add the following highlighted code.</span></span> <span data-ttu-id="406cd-816">新代码在 `.CreateTable( name: "Department"` 块后：</span><span class="sxs-lookup"><span data-stu-id="406cd-816">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="406cd-817">经过上面的更改，`Course` 行将在 `ComplexDataModel` `Up` 方法运行后与“临时”系建立联系。</span><span class="sxs-lookup"><span data-stu-id="406cd-817">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="406cd-818">生产应用可能：</span><span class="sxs-lookup"><span data-stu-id="406cd-818">A production app would:</span></span>

* <span data-ttu-id="406cd-819">包含用于将 `Department` 行和相关 `Course` 行添加到新 `Department` 行的代码或脚本。</span><span class="sxs-lookup"><span data-stu-id="406cd-819">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="406cd-820">不会使用“临时”系或 `Course.DepartmentID` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="406cd-820">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="406cd-821">下一教程将介绍相关数据。</span><span class="sxs-lookup"><span data-stu-id="406cd-821">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="406cd-822">其他资源</span><span class="sxs-lookup"><span data-stu-id="406cd-822">Additional resources</span></span>

* [<span data-ttu-id="406cd-823">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="406cd-823">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="406cd-824">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="406cd-824">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="406cd-825">[上一页](xref:data/ef-rp/migrations)
> [下一页](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="406cd-825">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
