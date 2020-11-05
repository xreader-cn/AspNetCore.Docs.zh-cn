---
title: '第 5 部分，ASP.NET Core 中的 :::no-loc(Razor)::: 页面和 EF Core - 数据模型'
author: rick-anderson
description: ':::no-loc(Razor)::: 页面和实体框架教程系列的第 5 部分。'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1ac9d6303daac82f3973c5d027fe1f453dc32e02
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054094"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="c4061-103">第 5 部分，ASP.NET Core 中的 :::no-loc(Razor)::: 页面和 EF Core - 数据模型</span><span class="sxs-lookup"><span data-stu-id="c4061-103">Part 5, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="c4061-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c4061-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c4061-105">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="c4061-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="c4061-106">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="c4061-106">In this tutorial:</span></span>

* <span data-ttu-id="c4061-107">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="c4061-108">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="c4061-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="c4061-109">完成的数据模型如下图所示：</span><span class="sxs-lookup"><span data-stu-id="c4061-109">The completed data model is shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="c4061-111">Student 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-111">The Student entity</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="c4061-113">使用以下代码替换 Models/Student.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="c4061-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="c4061-114">前面的代码将添加一个 `FullName` 属性，并将以下特性添加到现有属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="c4061-115">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="c4061-115">The FullName calculated property</span></span>

<span data-ttu-id="c4061-116">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="c4061-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="c4061-117">无法设置 `FullName`，因此它只有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="c4061-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="c4061-118">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="c4061-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="c4061-119">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="c4061-120">对于学生注册日期，目前所有页面都显示时间和日期，但只有日期是相关的。</span><span class="sxs-lookup"><span data-stu-id="c4061-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="c4061-121">使用数据注释特性，可更改一次代码，修复每个页面中数据的显示格式。</span><span class="sxs-lookup"><span data-stu-id="c4061-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="c4061-122">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="c4061-123">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="c4061-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="c4061-124">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="c4061-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="c4061-125">例如：</span><span class="sxs-lookup"><span data-stu-id="c4061-125">For example:</span></span>

* <span data-ttu-id="c4061-126">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="c4061-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="c4061-127">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="c4061-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="c4061-128">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性。</span><span class="sxs-lookup"><span data-stu-id="c4061-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="c4061-129">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="c4061-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="c4061-130">DisplayFormat 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="c4061-131">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="c4061-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="c4061-132">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="c4061-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="c4061-133">`DisplayFormat` 特性用于显式指定日期格式。</span><span class="sxs-lookup"><span data-stu-id="c4061-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="c4061-134">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="c4061-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="c4061-135">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="c4061-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="c4061-136">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="c4061-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="c4061-137">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="c4061-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="c4061-138">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="c4061-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="c4061-139">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="c4061-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="c4061-140">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="c4061-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="c4061-141">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="c4061-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="c4061-142">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接和客户端输入验证。</span><span class="sxs-lookup"><span data-stu-id="c4061-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="c4061-143">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="c4061-144">有关详细信息，请参阅 [\<input> 标记帮助程序文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="c4061-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="c4061-145">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="c4061-146">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c4061-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="c4061-147">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="c4061-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="c4061-148">显示的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="c4061-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="c4061-149">[稍后](#the-required-attribute)显示一个设置最小字符串长度的示例。</span><span class="sxs-lookup"><span data-stu-id="c4061-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="c4061-150">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="c4061-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="c4061-151">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="c4061-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="c4061-152">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="c4061-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="c4061-153">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 特性可用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="c4061-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="c4061-154">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="c4061-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4061-156">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器 。</span><span class="sxs-lookup"><span data-stu-id="c4061-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="c4061-158">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="c4061-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="c4061-159">名称字段具有类型 `nvarchar(MAX)`。</span><span class="sxs-lookup"><span data-stu-id="c4061-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="c4061-160">在本教程的后续部分创建和应用迁移时，因为字符串的长度特性，名称字段将变为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="c4061-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4061-162">在 SQLite 工具中检查 `Student` 表的列定义。</span><span class="sxs-lookup"><span data-stu-id="c4061-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="c4061-163">名称字段具有类型 `Text`。</span><span class="sxs-lookup"><span data-stu-id="c4061-163">The name fields have type `Text`.</span></span> <span data-ttu-id="c4061-164">请注意，首个名称字段名为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="c4061-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="c4061-165">下一节会将该列的名称更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="c4061-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="c4061-166">Column 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="c4061-167">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="c4061-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="c4061-168">在 `Student` 模型中，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="c4061-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="c4061-169">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="c4061-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="c4061-170">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="c4061-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="c4061-171">使用 `[Column]` 特性，数据模型中的 `Student.FirstMidName` 可映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="c4061-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="c4061-172">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="c4061-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="c4061-173">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="c4061-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="c4061-174">这种差异将通过在本教程后面添加迁移来解决。</span><span class="sxs-lookup"><span data-stu-id="c4061-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="c4061-175">Required 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="c4061-176">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="c4061-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="c4061-177">值类型（`DateTime`、`int` 和 `double`）等不可为 null 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="c4061-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="c4061-178">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="c4061-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="c4061-179">`Required` 特性必须与 `MinimumLength` 结合使用才能强制执行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="c4061-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="c4061-180">`MinimumLength` 和 `Required` 允许通过空格来满足验证。</span><span class="sxs-lookup"><span data-stu-id="c4061-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="c4061-181">使用 `RegularExpression` 特性来完全控制字符串。</span><span class="sxs-lookup"><span data-stu-id="c4061-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="c4061-182">Display 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="c4061-183">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="c4061-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="c4061-184">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="c4061-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="c4061-185">创建迁移</span><span class="sxs-lookup"><span data-stu-id="c4061-185">Create a migration</span></span>

<span data-ttu-id="c4061-186">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="c4061-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="c4061-187">此时引发异常。</span><span class="sxs-lookup"><span data-stu-id="c4061-187">An exception is thrown.</span></span> <span data-ttu-id="c4061-188">`[Column]` 特性导致 EF 希望查找名为 `FirstName` 的列，但数据库中的列名称仍为 `FirstMidName`。</span><span class="sxs-lookup"><span data-stu-id="c4061-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4061-190">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="c4061-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="c4061-191">在 PMC 中，输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="c4061-192">其中的第一个命令将生成以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c4061-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="c4061-193">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="c4061-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="c4061-194">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="c4061-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="c4061-195">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="c4061-195">Open the Student table in SSOX:</span></span>

  ![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="c4061-197">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="c4061-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="c4061-198">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="c4061-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="c4061-199">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="c4061-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4061-201">错误消息类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="c4061-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="c4061-202">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="c4061-202">Open a command window in the project folder.</span></span> <span data-ttu-id="c4061-203">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="c4061-204">数据库更新命令显示类似于以下示例的错误：</span><span class="sxs-lookup"><span data-stu-id="c4061-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="c4061-205">在本教程中，解决此错误的方法是删除并重新创建初始迁移。</span><span class="sxs-lookup"><span data-stu-id="c4061-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="c4061-206">有关详细信息，请参阅[迁移教程](xref:data/ef-rp/migrations)顶部的 SQLite 警告说明。</span><span class="sxs-lookup"><span data-stu-id="c4061-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="c4061-207">删除“Migrations”文件夹。</span><span class="sxs-lookup"><span data-stu-id="c4061-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="c4061-208">运行以下命令以删除数据库，创建新的初始迁移，并应用该迁移：</span><span class="sxs-lookup"><span data-stu-id="c4061-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="c4061-209">在 SQLite 工具中检查 Student 表。</span><span class="sxs-lookup"><span data-stu-id="c4061-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="c4061-210">以前为 FirstMidName 的列现在为 FirstName。</span><span class="sxs-lookup"><span data-stu-id="c4061-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="c4061-211">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="c4061-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="c4061-212">请注意，时间和日期既未输入，也未显示。</span><span class="sxs-lookup"><span data-stu-id="c4061-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="c4061-213">选择“新建”，尝试输入不超过 50 个字符的名称。</span><span class="sxs-lookup"><span data-stu-id="c4061-213">Select **Create New** , and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="c4061-214">在以下部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="c4061-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="c4061-215">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="c4061-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="c4061-216">Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-216">The Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="c4061-218">用以下代码创建 Models/Instructor.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="c4061-219">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="c4061-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="c4061-220">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="c4061-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="c4061-221">导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-221">Navigation properties</span></span>

<span data-ttu-id="c4061-222">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="c4061-223">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="c4061-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c4061-224">讲师最多可以有一个办公室，因此 `OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="c4061-225">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="c4061-226">OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-226">The OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="c4061-228">用以下代码创建 Models/OfficeAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="c4061-229">Key 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-229">The Key attribute</span></span>

<span data-ttu-id="c4061-230">`[Key]` 特性用于在属性名不是 classnameID 或 ID 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="c4061-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="c4061-231">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="c4061-232">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="c4061-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="c4061-233">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="c4061-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="c4061-234">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为 `InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="c4061-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="c4061-235">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="c4061-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="c4061-236">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="c4061-237">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-237">The Instructor navigation property</span></span>

<span data-ttu-id="c4061-238">`Instructor.OfficeAssignment` 导航属性可以为 null，因为给定的讲师可能没有 `OfficeAssignment` 行。</span><span class="sxs-lookup"><span data-stu-id="c4061-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="c4061-239">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="c4061-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="c4061-240">`OfficeAssignment.Instructor` 导航属性将始终具有一个 Instructor 实体，因为外键 `InstructorID` 类型为 `int`，不可为 null 的值类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="c4061-241">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="c4061-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="c4061-242">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="c4061-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="c4061-243">Course 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-243">The Course Entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="c4061-245">用以下代码更新 *Models/Course.cs* ：</span><span class="sxs-lookup"><span data-stu-id="c4061-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="c4061-246">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="c4061-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="c4061-247">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="c4061-248">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="c4061-249">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有外键属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="c4061-250">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="c4061-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="c4061-251">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="c4061-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="c4061-252">然而，在数据模型中显式包含 FK 可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="c4061-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="c4061-253">例如，假设某个模型中不包含 FK 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="c4061-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="c4061-254">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="c4061-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="c4061-255">如果未显式加载，则 `Department` 属性为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="c4061-256">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="c4061-257">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="c4061-258">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="c4061-259">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="c4061-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="c4061-260">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="c4061-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="c4061-261">数据库生成通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="c4061-262">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="c4061-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="c4061-263">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="c4061-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="c4061-264">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="c4061-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="c4061-265">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="c4061-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="c4061-266">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="c4061-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c4061-267">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="c4061-268">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="c4061-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="c4061-269">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="c4061-270">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="c4061-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="c4061-271">一门课程可能由多位讲师讲授，因此 `CourseAssignments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="c4061-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c4061-272">`CourseAssignment` 在[后文](#many-to-many-relationships)介绍。</span><span class="sxs-lookup"><span data-stu-id="c4061-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="c4061-273">Department 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-273">The Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="c4061-275">用以下代码创建 Models/Department.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="c4061-276">Column 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-276">The Column attribute</span></span>

<span data-ttu-id="c4061-277">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="c4061-278">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="c4061-279">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="c4061-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="c4061-280">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-280">Column mapping is generally not required.</span></span> <span data-ttu-id="c4061-281">EF Core 基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="c4061-282">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="c4061-283">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="c4061-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c4061-284">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="c4061-285">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="c4061-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="c4061-286">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="c4061-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="c4061-287">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="c4061-287">An administrator is always an instructor.</span></span> <span data-ttu-id="c4061-288">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="c4061-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="c4061-289">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="c4061-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="c4061-290">上面代码中的问号 (?) 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="c4061-291">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="c4061-292">按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="c4061-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="c4061-293">此默认行为可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="c4061-294">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="c4061-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="c4061-295">例如，如果将 `Department.InstructorID` 属性定义为不可为 null，EF Core 将配置级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="c4061-296">在这种情况下，指定为管理员的讲师被删除时，该学院将被删除。</span><span class="sxs-lookup"><span data-stu-id="c4061-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="c4061-297">在这种情况下，限制规则将更有意义。</span><span class="sxs-lookup"><span data-stu-id="c4061-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="c4061-298">以下 [fluent API](#fluent-api-alternative-to-attributes) 将设置限制规则并禁用级联规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="c4061-299">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-299">The Enrollment entity</span></span>

<span data-ttu-id="c4061-300">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="c4061-300">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="c4061-302">用以下代码更新 *Models/Enrollment.cs* ：</span><span class="sxs-lookup"><span data-stu-id="c4061-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c4061-303">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="c4061-304">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="c4061-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="c4061-305">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="c4061-306">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="c4061-307">多对多关系</span><span class="sxs-lookup"><span data-stu-id="c4061-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="c4061-308">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="c4061-309">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表。</span><span class="sxs-lookup"><span data-stu-id="c4061-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="c4061-310">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据（本教程中为 PK 和 `Grade`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="c4061-311">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="c4061-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="c4061-312">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="c4061-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="c4061-313">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="c4061-313">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="c4061-315">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="c4061-316">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="c4061-317">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="c4061-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="c4061-318">`Instructor` 和 `Course` 实体具有使用纯联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="c4061-319">注意：EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="c4061-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="c4061-320">有关详细信息，请参阅 [EF Core 2.0 中的多对多关系](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。</span><span class="sxs-lookup"><span data-stu-id="c4061-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="c4061-321">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-321">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="c4061-323">用以下代码创建 Models/CourseAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="c4061-324">“讲师-课程”的多对多关系要求使用联接表，而该联接表的实体则为 CourseAssignment。</span><span class="sxs-lookup"><span data-stu-id="c4061-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="c4061-326">常规做法是将联接实体命名为 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="c4061-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="c4061-327">例如，使用此模式的“讲师-课程”联接表将是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="c4061-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="c4061-328">但是，我们建议使用可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="c4061-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="c4061-329">数据模型开始时很简单，其内容会逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="c4061-329">Data models start out simple and grow.</span></span> <span data-ttu-id="c4061-330">无有效负载的联接表 (PJT) 通常会发展为包含有效负载。</span><span class="sxs-lookup"><span data-stu-id="c4061-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="c4061-331">该名称以描述性实体名称开始，因此不需要随联接表更改而更改。</span><span class="sxs-lookup"><span data-stu-id="c4061-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="c4061-332">理想情况下，联接实体在业务领域中可能具有专业名称（可能是一个词）。</span><span class="sxs-lookup"><span data-stu-id="c4061-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="c4061-333">例如，可以使用名为“阅读率”的联接实体链接“书籍”和“读客”。</span><span class="sxs-lookup"><span data-stu-id="c4061-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="c4061-334">对于“讲师-课程”的多对多关系，使用 `CourseAssignment` 比使用`CourseInstructor`更合适。</span><span class="sxs-lookup"><span data-stu-id="c4061-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="c4061-335">组合键</span><span class="sxs-lookup"><span data-stu-id="c4061-335">Composite key</span></span>

<span data-ttu-id="c4061-336">`CourseAssignment` 中的两个 FK（`InstructorID` 和 `CourseID`）共同唯一标识 `CourseAssignment` 表的每一行。</span><span class="sxs-lookup"><span data-stu-id="c4061-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="c4061-337">`CourseAssignment` 不需要专用的 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="c4061-338">`InstructorID` 和 `CourseID` 属性充当组合 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="c4061-339">使用 Fluent API 是向 EF Core 指定组合 PK 的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="c4061-340">下一部分演示如何配置组合 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="c4061-341">组合键可确保：</span><span class="sxs-lookup"><span data-stu-id="c4061-341">The composite key ensures that:</span></span>

* <span data-ttu-id="c4061-342">允许一门课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="c4061-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="c4061-343">允许一名讲师对应多行。</span><span class="sxs-lookup"><span data-stu-id="c4061-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="c4061-344">不允许同一讲师和课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="c4061-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="c4061-345">`Enrollment` 联接实体定义其自己的 PK，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="c4061-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="c4061-346">若要防止此类重复：</span><span class="sxs-lookup"><span data-stu-id="c4061-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="c4061-347">请在 FK 字段上添加唯一索引，或</span><span class="sxs-lookup"><span data-stu-id="c4061-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="c4061-348">配置具有主要组合键（与 `CourseAssignment` 类似）的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="c4061-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="c4061-349">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="c4061-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="c4061-350">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c4061-350">Update the database context</span></span>

<span data-ttu-id="c4061-351">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="c4061-352">上面的代码添加新实体并配置 `CourseAssignment` 实体的组合 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="c4061-353">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="c4061-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="c4061-354">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为。</span><span class="sxs-lookup"><span data-stu-id="c4061-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="c4061-355">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="c4061-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="c4061-356">[下面的代码](/ef/core/modeling/#use-fluent-api-to-configure-a-model)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="c4061-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="c4061-357">在本教程中，fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="c4061-358">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="c4061-359">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="c4061-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="c4061-360">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="c4061-361">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="c4061-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="c4061-362">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="c4061-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="c4061-363">某些配置只能通过 Fluent API 完成（指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="c4061-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="c4061-364">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="c4061-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="c4061-365">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="c4061-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="c4061-366">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="c4061-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="c4061-367">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="c4061-368">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="c4061-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="c4061-369">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="c4061-370">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="c4061-371">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="c4061-372">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="c4061-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="c4061-373">实体关系图</span><span class="sxs-lookup"><span data-stu-id="c4061-373">Entity diagram</span></span>

<span data-ttu-id="c4061-374">下图显示 EF Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="c4061-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="c4061-376">上面的关系图显示：</span><span class="sxs-lookup"><span data-stu-id="c4061-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="c4061-377">几条一对多关系线（1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="c4061-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="c4061-378">`Instructor` 和 `OfficeAssignment` 实体之间的一对零或一关系线（1 到 0..1）。</span><span class="sxs-lookup"><span data-stu-id="c4061-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="c4061-379">`Instructor` 和 `Department` 实体之间的零或一到多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="c4061-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="c4061-380">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="c4061-380">Seed the database</span></span>

<span data-ttu-id="c4061-381">更新 Data/DbInitializer.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="c4061-381">Update the code in *Data/DbInitializer.cs* :</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="c4061-382">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="c4061-383">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="c4061-384">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="c4061-384">The sample data is used for testing.</span></span> <span data-ttu-id="c4061-385">有关如何对多对多联接表进行种子设定的示例，请参阅 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="c4061-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="c4061-386">添加迁移</span><span class="sxs-lookup"><span data-stu-id="c4061-386">Add a migration</span></span>

<span data-ttu-id="c4061-387">生成项目。</span><span class="sxs-lookup"><span data-stu-id="c4061-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4061-389">在 PMC 中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="c4061-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="c4061-390">前面的命令显示可能存在数据丢失的相关警告。</span><span class="sxs-lookup"><span data-stu-id="c4061-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="c4061-391">如果运行 `database update` 命令，则会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="c4061-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="c4061-392">下一节将介绍如何处理此错误。</span><span class="sxs-lookup"><span data-stu-id="c4061-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4061-394">如果添加迁移并运行 `database update` 命令，则将生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="c4061-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="c4061-395">下一节将介绍如何避免此错误。</span><span class="sxs-lookup"><span data-stu-id="c4061-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="c4061-396">应用迁移或删除并重新创建</span><span class="sxs-lookup"><span data-stu-id="c4061-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="c4061-397">现已有一个数据库，需要考虑如何将更改应用到其中。</span><span class="sxs-lookup"><span data-stu-id="c4061-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="c4061-398">本教程演示两种替代方法：</span><span class="sxs-lookup"><span data-stu-id="c4061-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="c4061-399">[删除并重新创建数据库](#drop)。</span><span class="sxs-lookup"><span data-stu-id="c4061-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="c4061-400">如果使用 SQLite，请选择此部分。</span><span class="sxs-lookup"><span data-stu-id="c4061-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="c4061-401">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="c4061-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="c4061-402">本部分的说明仅适用于 SQL Server，不适用于 SQLite。</span><span class="sxs-lookup"><span data-stu-id="c4061-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="c4061-403">这两个选项都适用于 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="c4061-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="c4061-404">虽然应用迁移方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c4061-405">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="c4061-405">Drop and re-create the database</span></span>

<span data-ttu-id="c4061-406">如果使用 SQL Server 并且想要在以下部分执行应用迁移方法，[请跳过此部分](#apply-the-migration)。</span><span class="sxs-lookup"><span data-stu-id="c4061-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="c4061-407">若要强制 EF Core 创建新的数据库，请删除并更新该数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4061-409">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c4061-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="c4061-410">删除“Migrations”文件夹，然后运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c4061-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4061-412">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="c4061-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="c4061-413">项目文件夹包含 ContosoUniversity.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="c4061-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="c4061-414">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="c4061-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="c4061-415">删除“Migrations”文件夹，然后运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c4061-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="c4061-416">运行应用。</span><span class="sxs-lookup"><span data-stu-id="c4061-416">Run the app.</span></span> <span data-ttu-id="c4061-417">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="c4061-418">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="c4061-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4061-420">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="c4061-421">如果之前已打开过 SSOX，请单击“刷新”按钮。</span><span class="sxs-lookup"><span data-stu-id="c4061-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="c4061-422">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="c4061-422">Expand the **Tables** node.</span></span> <span data-ttu-id="c4061-423">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="c4061-423">The created tables are displayed.</span></span>

  ![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="c4061-425">查看 CourseAssignment 表：</span><span class="sxs-lookup"><span data-stu-id="c4061-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="c4061-426">右键单击 CourseAssignment 表，然后选择“查看数据” 。</span><span class="sxs-lookup"><span data-stu-id="c4061-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="c4061-427">验证 CourseAssignment 表包含数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4061-430">使用 SQLite 工具检查数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="c4061-431">新的表和列。</span><span class="sxs-lookup"><span data-stu-id="c4061-431">New tables and columns.</span></span>
* <span data-ttu-id="c4061-432">表中设定种子的数据，例如 CourseAssignment 表。</span><span class="sxs-lookup"><span data-stu-id="c4061-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="c4061-433">应用迁移</span><span class="sxs-lookup"><span data-stu-id="c4061-433">Apply the migration</span></span>

<span data-ttu-id="c4061-434">本部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="c4061-434">This section is optional.</span></span> <span data-ttu-id="c4061-435">这些步骤仅适用于 SQL Server LocalDB，并且仅当跳过前面的[删除并重新创建数据库](#drop)部分时才适用。</span><span class="sxs-lookup"><span data-stu-id="c4061-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="c4061-436">当现有数据与迁移一起运行时，可能存在不满足现有数据的 FK 约束。</span><span class="sxs-lookup"><span data-stu-id="c4061-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="c4061-437">使用生产数据时，必须采取步骤来迁移现有数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="c4061-438">本部分提供修复 FK 约束冲突的示例。</span><span class="sxs-lookup"><span data-stu-id="c4061-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="c4061-439">务必在备份后执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="c4061-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="c4061-440">如果已完成上述[删除并重新创建数据库](#drop)部分，请不要更改这些代码。</span><span class="sxs-lookup"><span data-stu-id="c4061-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="c4061-441">{timestamp}_ComplexDataModel.cs 文件包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="c4061-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="c4061-442">上面的代码将向 `Course` 表添加不可为 NULL 的 `DepartmentID` FK。</span><span class="sxs-lookup"><span data-stu-id="c4061-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="c4061-443">前面教程中的数据库在 `Course` 中包含行，以便迁移时不会更新表。</span><span class="sxs-lookup"><span data-stu-id="c4061-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="c4061-444">若要使 `ComplexDataModel` 迁移可与现有数据搭配运行：</span><span class="sxs-lookup"><span data-stu-id="c4061-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="c4061-445">请更改代码以便为新列 (`DepartmentID`) 赋予默认值。</span><span class="sxs-lookup"><span data-stu-id="c4061-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="c4061-446">创建名为“临时”的虚拟系来充当默认的系。</span><span class="sxs-lookup"><span data-stu-id="c4061-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="c4061-447">修复外键约束</span><span class="sxs-lookup"><span data-stu-id="c4061-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="c4061-448">在 `ComplexDataModel` 迁移类中，更新 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="c4061-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="c4061-449">打开 {timestamp}_ComplexDataModel.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="c4061-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="c4061-450">对将 `DepartmentID` 列添加到 `Course` 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="c4061-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="c4061-451">添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="c4061-451">Add the following highlighted code.</span></span> <span data-ttu-id="c4061-452">新代码在 `.CreateTable( name: "Department"` 块后：</span><span class="sxs-lookup"><span data-stu-id="c4061-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="c4061-453">经过上面的更改，现有的 `Course` 行将在 `ComplexDataModel.Up` 方法运行后与“临时”院系建立联系。</span><span class="sxs-lookup"><span data-stu-id="c4061-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="c4061-454">本教程简化了此处所示的处理方式。</span><span class="sxs-lookup"><span data-stu-id="c4061-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="c4061-455">生产应用可能：</span><span class="sxs-lookup"><span data-stu-id="c4061-455">A production app would:</span></span>

* <span data-ttu-id="c4061-456">包含用于将 `Department` 行和相关 `Course` 行添加到新 `Department` 行的代码或脚本。</span><span class="sxs-lookup"><span data-stu-id="c4061-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="c4061-457">不会使用“临时”系或 `Course.DepartmentID` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="c4061-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c4061-459">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c4061-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="c4061-460">由于 `DbInitializer.Initialize` 方法仅适用于空数据库，因此请使用 SSOX 删除 Student 表和 Course 表中的所有行。</span><span class="sxs-lookup"><span data-stu-id="c4061-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="c4061-461">（级联删除将负责 Enrollment 表。）</span><span class="sxs-lookup"><span data-stu-id="c4061-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c4061-463">如果在 Visual Studio Code 中使用 SQL Server LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c4061-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="c4061-464">运行应用。</span><span class="sxs-lookup"><span data-stu-id="c4061-464">Run the app.</span></span> <span data-ttu-id="c4061-465">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="c4061-466">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="c4061-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c4061-467">后续步骤</span><span class="sxs-lookup"><span data-stu-id="c4061-467">Next steps</span></span>

<span data-ttu-id="c4061-468">接下来的两个教程演示如何读取和更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c4061-469">[上一个教程](xref:data/ef-rp/migrations)
> [下一个教程](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="c4061-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c4061-470">前面的教程介绍了由三个实体组成的基本数据模型。</span><span class="sxs-lookup"><span data-stu-id="c4061-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="c4061-471">本教程将演示如何：</span><span class="sxs-lookup"><span data-stu-id="c4061-471">In this tutorial:</span></span>

* <span data-ttu-id="c4061-472">添加更多实体和关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="c4061-473">通过指定格式设置、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="c4061-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="c4061-474">已完成数据模型的实体类如下图所示：</span><span class="sxs-lookup"><span data-stu-id="c4061-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="c4061-476">如果遇到无法解决的问题，请下载[已完成应用](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="c4061-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="c4061-477">使用特性自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="c4061-477">Customize the data model with attributes</span></span>

<span data-ttu-id="c4061-478">此部分将使用特性自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="c4061-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="c4061-479">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-479">The DataType attribute</span></span>

<span data-ttu-id="c4061-480">学生页面当前显示注册日期。</span><span class="sxs-lookup"><span data-stu-id="c4061-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="c4061-481">通常情况下，日期字段仅显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="c4061-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="c4061-482">用以下突出显示的代码更新 *Models/Student.cs* ：</span><span class="sxs-lookup"><span data-stu-id="c4061-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="c4061-483">[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 特性指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="c4061-484">在此情况下，应仅显示日期，而不是日期加时间。</span><span class="sxs-lookup"><span data-stu-id="c4061-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="c4061-485">[DataType 枚举](/dotnet/api/system.componentmodel.dataannotations.datatype)提供多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。应用还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="c4061-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="c4061-486">例如：</span><span class="sxs-lookup"><span data-stu-id="c4061-486">For example:</span></span>

* <span data-ttu-id="c4061-487">`mailto:` 链接将依据 `DataType.EmailAddress` 自动创建。</span><span class="sxs-lookup"><span data-stu-id="c4061-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="c4061-488">大多数浏览器中都提供面向 `DataType.Date` 的日期选择器。</span><span class="sxs-lookup"><span data-stu-id="c4061-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="c4061-489">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。</span><span class="sxs-lookup"><span data-stu-id="c4061-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="c4061-490">`DataType` 特性不提供验证。</span><span class="sxs-lookup"><span data-stu-id="c4061-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="c4061-491">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="c4061-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="c4061-492">默认情况下，日期字段根据基于服务器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="c4061-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="c4061-493">`DisplayFormat` 特性用于显式指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="c4061-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="c4061-494">`ApplyFormatInEditMode` 设置指定还应对编辑 UI 应用该格式设置。</span><span class="sxs-lookup"><span data-stu-id="c4061-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="c4061-495">某些字段不应使用 `ApplyFormatInEditMode`。</span><span class="sxs-lookup"><span data-stu-id="c4061-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="c4061-496">例如，编辑文本框中通常不应显示货币符号。</span><span class="sxs-lookup"><span data-stu-id="c4061-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="c4061-497">`DisplayFormat` 特性可由其本身使用。</span><span class="sxs-lookup"><span data-stu-id="c4061-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="c4061-498">搭配使用 `DataType` 特性和 `DisplayFormat` 特性通常是很好的做法。</span><span class="sxs-lookup"><span data-stu-id="c4061-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="c4061-499">`DataType` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="c4061-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="c4061-500">`DataType` 特性可提供 `DisplayFormat` 中所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="c4061-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="c4061-501">浏览器可启用 HTML5 功能。</span><span class="sxs-lookup"><span data-stu-id="c4061-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="c4061-502">例如，显示日历控件、区域设置适用的货币符号、电子邮件链接、客户端输入验证等。</span><span class="sxs-lookup"><span data-stu-id="c4061-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="c4061-503">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="c4061-504">有关详细信息，请参阅 [\<input> 标记帮助程序文档](xref:mvc/views/working-with-forms#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="c4061-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="c4061-505">运行应用。</span><span class="sxs-lookup"><span data-stu-id="c4061-505">Run the app.</span></span> <span data-ttu-id="c4061-506">导航到学生索引页。</span><span class="sxs-lookup"><span data-stu-id="c4061-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="c4061-507">将不再显示时间。</span><span class="sxs-lookup"><span data-stu-id="c4061-507">Times are no longer displayed.</span></span> <span data-ttu-id="c4061-508">使用 `Student` 模型的每个视图将显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="c4061-508">Every view that uses the `Student` model displays the date without time.</span></span>

![“学生”索引页显示不带时间的日期](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="c4061-510">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-510">The StringLength attribute</span></span>

<span data-ttu-id="c4061-511">可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c4061-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="c4061-512">[StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 特性指定数据字段中允许的字符的最小长度和最大长度。</span><span class="sxs-lookup"><span data-stu-id="c4061-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="c4061-513">`StringLength` 特性还提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="c4061-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="c4061-514">最小值对数据库架构没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="c4061-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="c4061-515">使用以下代码更新 `Student` 模型：</span><span class="sxs-lookup"><span data-stu-id="c4061-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="c4061-516">上面的代码将名称限制为不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="c4061-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="c4061-517">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="c4061-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="c4061-518">[RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 特性用于向输入应用限制。</span><span class="sxs-lookup"><span data-stu-id="c4061-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="c4061-519">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="c4061-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="c4061-520">运行应用：</span><span class="sxs-lookup"><span data-stu-id="c4061-520">Run the app:</span></span>

* <span data-ttu-id="c4061-521">导航到学生页。</span><span class="sxs-lookup"><span data-stu-id="c4061-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="c4061-522">选择“新建”并输入不超过 50 个字符的名称。</span><span class="sxs-lookup"><span data-stu-id="c4061-522">Select **Create New** , and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="c4061-523">选择“创建”时，客户端验证会显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="c4061-523">Select **Create** , client-side validation shows an error message.</span></span>

![显示字符串长度错误的“学生索引”页](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="c4061-525">在“SQL Server 对象资源管理器”(SSOX) 中，双击 Student 表，打开 Student 表设计器 。</span><span class="sxs-lookup"><span data-stu-id="c4061-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移前 SSOX 中的 Student 表](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="c4061-527">上图显示 `Student` 表的架构。</span><span class="sxs-lookup"><span data-stu-id="c4061-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="c4061-528">名称字段的类型为 `nvarchar(MAX)`，因为数据库上尚未运行迁移。</span><span class="sxs-lookup"><span data-stu-id="c4061-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="c4061-529">稍后在本教程中运行迁移时，名称字段将变成 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="c4061-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="c4061-530">Column 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-530">The Column attribute</span></span>

<span data-ttu-id="c4061-531">特性可以控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="c4061-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="c4061-532">在本部分，`Column` 特性用于将 `FirstMidName` 属性的名称映射到数据库中的“FirstName”。</span><span class="sxs-lookup"><span data-stu-id="c4061-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="c4061-533">创建数据库后，模型上的属性名将用作列名（使用 `Column` 特性时除外）。</span><span class="sxs-lookup"><span data-stu-id="c4061-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="c4061-534">`Student` 模型使用 `FirstMidName` 作为名字字段，因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="c4061-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="c4061-535">用以下突出显示的代码更新 *Student.cs* 文件：</span><span class="sxs-lookup"><span data-stu-id="c4061-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="c4061-536">进行上述更改后，应用中的 `Student.FirstMidName` 将映射到 `Student` 表的 `FirstName` 列。</span><span class="sxs-lookup"><span data-stu-id="c4061-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="c4061-537">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变。</span><span class="sxs-lookup"><span data-stu-id="c4061-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="c4061-538">`SchoolContext` 的支持模型将不再与数据库匹配。</span><span class="sxs-lookup"><span data-stu-id="c4061-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="c4061-539">如果在执行迁移前运行应用，则会生成如下异常：</span><span class="sxs-lookup"><span data-stu-id="c4061-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="c4061-540">若要更新数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-540">To update the DB:</span></span>

* <span data-ttu-id="c4061-541">生成项目。</span><span class="sxs-lookup"><span data-stu-id="c4061-541">Build the project.</span></span>
* <span data-ttu-id="c4061-542">在项目文件夹中打开命令窗口。</span><span class="sxs-lookup"><span data-stu-id="c4061-542">Open a command window in the project folder.</span></span> <span data-ttu-id="c4061-543">输入以下命令以创建新迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="c4061-546">`migrations add ColumnFirstName` 命令将生成如下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c4061-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="c4061-547">生成警告的原因是名称字段现已限制为 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="c4061-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="c4061-548">如果数据库中的名称超过 50 个字符，则第 51 个字符及后面的所有字符都将丢失。</span><span class="sxs-lookup"><span data-stu-id="c4061-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="c4061-549">测试应用。</span><span class="sxs-lookup"><span data-stu-id="c4061-549">Test the app.</span></span>

<span data-ttu-id="c4061-550">在 SSOX 中打开 Student 表：</span><span class="sxs-lookup"><span data-stu-id="c4061-550">Open the Student table in SSOX:</span></span>

![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="c4061-552">执行迁移前，名称列的类型为 [nvarchar (MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="c4061-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="c4061-553">名称列现在的类型为 `nvarchar(50)`。</span><span class="sxs-lookup"><span data-stu-id="c4061-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="c4061-554">列名已从 `FirstMidName` 更改为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="c4061-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="c4061-555">在下一部分中，在某些阶段生成应用会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="c4061-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="c4061-556">说明用于指定生成应用的时间。</span><span class="sxs-lookup"><span data-stu-id="c4061-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="c4061-557">Student 实体更新</span><span class="sxs-lookup"><span data-stu-id="c4061-557">Student entity update</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="c4061-559">用以下代码更新 *Models/Student.cs* ：</span><span class="sxs-lookup"><span data-stu-id="c4061-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="c4061-560">Required 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-560">The Required attribute</span></span>

<span data-ttu-id="c4061-561">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="c4061-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="c4061-562">值类型（`DateTime`、`int`、`double`）等不可为 NULL 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="c4061-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="c4061-563">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="c4061-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="c4061-564">不能用 `StringLength` 特性中的最短长度参数替换 `Required` 特性：</span><span class="sxs-lookup"><span data-stu-id="c4061-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="c4061-565">Display 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-565">The Display attribute</span></span>

<span data-ttu-id="c4061-566">`Display` 特性指定文本框的标题栏应为“FirstName”、“LastName”、“FullName”和“EnrollmentDate”。</span><span class="sxs-lookup"><span data-stu-id="c4061-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="c4061-567">标题栏默认不使用空格分隔词语，如“Lastname”。</span><span class="sxs-lookup"><span data-stu-id="c4061-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="c4061-568">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="c4061-568">The FullName calculated property</span></span>

<span data-ttu-id="c4061-569">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="c4061-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="c4061-570">`FullName` 不能设置并且仅具有一个 get 访问器。</span><span class="sxs-lookup"><span data-stu-id="c4061-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="c4061-571">数据库中不会创建任何 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="c4061-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="c4061-572">创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-572">Create the Instructor Entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="c4061-574">用以下代码创建 Models/Instructor.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="c4061-575">一行可包含多个特性。</span><span class="sxs-lookup"><span data-stu-id="c4061-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="c4061-576">可按如下方式编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="c4061-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="c4061-577">CourseAssignments 和 OfficeAssignment 导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="c4061-578">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="c4061-579">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="c4061-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c4061-580">如果导航属性包含多个实体：</span><span class="sxs-lookup"><span data-stu-id="c4061-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="c4061-581">它必须是可在其中添加、删除和更新实体的列表类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="c4061-582">导航属性类型包括：</span><span class="sxs-lookup"><span data-stu-id="c4061-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="c4061-583">如果指定了 `ICollection<T>`，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="c4061-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="c4061-584">`CourseAssignment` 实体在“多对多关系”部分进行介绍。</span><span class="sxs-lookup"><span data-stu-id="c4061-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="c4061-585">Contoso University 业务规则规定一名讲师最多可获得一间办公室。</span><span class="sxs-lookup"><span data-stu-id="c4061-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="c4061-586">`OfficeAssignment` 属性包含一个 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="c4061-587">如果未分配办公室，则 `OfficeAssignment` 为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="c4061-588">创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-588">Create the OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="c4061-590">用以下代码创建 Models/OfficeAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="c4061-591">Key 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-591">The Key attribute</span></span>

<span data-ttu-id="c4061-592">`[Key]` 特性用于在属性名不是 classnameID 或 ID 时将属性标识为主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="c4061-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="c4061-593">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="c4061-594">仅当与分配到办公室的讲师之间建立关系时才存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="c4061-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="c4061-595">`OfficeAssignment` PK 也是其到 `Instructor` 实体的外键 (FK)。</span><span class="sxs-lookup"><span data-stu-id="c4061-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="c4061-596">EF Core 无法自动将 `InstructorID` 识别为 `OfficeAssignment` 的 PK，因为：</span><span class="sxs-lookup"><span data-stu-id="c4061-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="c4061-597">`InstructorID` 不遵循 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="c4061-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="c4061-598">因此，`Key` 特性用于将 `InstructorID` 识别为 PK：</span><span class="sxs-lookup"><span data-stu-id="c4061-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="c4061-599">默认情况下，EF Core 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="c4061-600">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-600">The Instructor navigation property</span></span>

<span data-ttu-id="c4061-601">`Instructor` 实体的 `OfficeAssignment` 导航属性可以为 NULL，因为：</span><span class="sxs-lookup"><span data-stu-id="c4061-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="c4061-602">引用类型（例如，类可以为 NULL）。</span><span class="sxs-lookup"><span data-stu-id="c4061-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="c4061-603">一名讲师可能没有办公室分配。</span><span class="sxs-lookup"><span data-stu-id="c4061-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="c4061-604">`OfficeAssignment` 实体具有不可为 NULL 的 `Instructor` 导航属性，因为：</span><span class="sxs-lookup"><span data-stu-id="c4061-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="c4061-605">`InstructorID` 不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="c4061-606">没有讲师则不可能存在办公室分配。</span><span class="sxs-lookup"><span data-stu-id="c4061-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="c4061-607">当 `Instructor` 实体具有相关 `OfficeAssignment` 实体时，每个实体都具有对其导航属性中的另一个实体的引用。</span><span class="sxs-lookup"><span data-stu-id="c4061-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="c4061-608">`[Required]` 特性可以应用于 `Instructor` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="c4061-609">上面的代码指定必须存在相关的讲师。</span><span class="sxs-lookup"><span data-stu-id="c4061-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="c4061-610">上面的代码没有必要，因为 `InstructorID` 外键（也是 PK）不可为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="c4061-611">修改 Course 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-611">Modify the Course Entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="c4061-613">用以下代码更新 *Models/Course.cs* ：</span><span class="sxs-lookup"><span data-stu-id="c4061-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="c4061-614">`Course` 实体具有外键 (FK) 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="c4061-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="c4061-615">`DepartmentID` 指向相关的 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="c4061-616">`Course` 实体具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="c4061-617">当数据模型具有相关实体的导航属性时，EF Core 不要求此模型具有 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="c4061-618">EF Core 可在数据库中的任何所需位置自动创建 FK。</span><span class="sxs-lookup"><span data-stu-id="c4061-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="c4061-619">EF Core 为自动创建的 FK 创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="c4061-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="c4061-620">数据模型中包含 FK 后可使更新更简单和更高效。</span><span class="sxs-lookup"><span data-stu-id="c4061-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="c4061-621">例如，假设某个模型中不包含 FK 属性 `DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="c4061-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="c4061-622">当提取 Course 实体进行编辑时：</span><span class="sxs-lookup"><span data-stu-id="c4061-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="c4061-623">如果未显式加载 `Department` 实体，则该实体将为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="c4061-624">若要更新 Course 实体，则必须先提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="c4061-625">如果数据模型中包含 FK 属性 `DepartmentID`，则无需在更新前提取 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="c4061-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="c4061-626">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="c4061-627">`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 特性指定 PK 由应用程序提供而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="c4061-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="c4061-628">默认情况下，EF Core 假定 PK 值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="c4061-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="c4061-629">由数据库生成 PK 值通常是最佳方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="c4061-630">`Course` 实体的 PK 由用户指定。</span><span class="sxs-lookup"><span data-stu-id="c4061-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="c4061-631">例如，对于课程编号，数学系可以使用 1000 系列的编号，英语系可以使用 2000 系列的编号。</span><span class="sxs-lookup"><span data-stu-id="c4061-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="c4061-632">`DatabaseGenerated` 特性还可用于生成默认值。</span><span class="sxs-lookup"><span data-stu-id="c4061-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="c4061-633">例如，数据库可以自动生成日期字段以记录数据行的创建或更新日期。</span><span class="sxs-lookup"><span data-stu-id="c4061-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="c4061-634">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="c4061-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c4061-635">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="c4061-636">`Course` 实体中的外键 (FK) 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="c4061-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="c4061-637">课程将分配到一个系，因此将存在 `DepartmentID` FK 和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="c4061-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="c4061-638">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="c4061-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="c4061-639">一门课程可能由多位讲师讲授，因此 `CourseAssignments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="c4061-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="c4061-640">`CourseAssignment` 在[后文](#many-to-many-relationships)介绍。</span><span class="sxs-lookup"><span data-stu-id="c4061-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="c4061-641">创建 Department 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-641">Create the Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="c4061-643">用以下代码创建 Models/Department.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="c4061-644">Column 特性</span><span class="sxs-lookup"><span data-stu-id="c4061-644">The Column attribute</span></span>

<span data-ttu-id="c4061-645">`Column` 特性以前用于更改列名映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="c4061-646">在 `Department` 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="c4061-647">`Budget` 列通过数据库中的 SQL Server 货币类型进行定义：</span><span class="sxs-lookup"><span data-stu-id="c4061-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="c4061-648">通常不需要列映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-648">Column mapping is generally not required.</span></span> <span data-ttu-id="c4061-649">EF Core 通常基于属性的 CLR 类型选择相应的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="c4061-650">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="c4061-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="c4061-651">`Budget` 用于货币，但货币数据类型更适合货币。</span><span class="sxs-lookup"><span data-stu-id="c4061-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c4061-652">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="c4061-653">FK 和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="c4061-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="c4061-654">一个系可能有也可能没有管理员。</span><span class="sxs-lookup"><span data-stu-id="c4061-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="c4061-655">管理员始终由讲师担任。</span><span class="sxs-lookup"><span data-stu-id="c4061-655">An administrator is always an instructor.</span></span> <span data-ttu-id="c4061-656">因此，`InstructorID` 属性作为到 `Instructor` 实体的 FK 包含在其中。</span><span class="sxs-lookup"><span data-stu-id="c4061-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="c4061-657">导航属性名为 `Administrator`，但其中包含 `Instructor` 实体：</span><span class="sxs-lookup"><span data-stu-id="c4061-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="c4061-658">上面代码中的问号 (?) 指定属性可以为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="c4061-659">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="c4061-660">注意：按照约定，EF Core 能针对不可为 NULL 的 FK 和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="c4061-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="c4061-661">级联删除可能导致形成循环级联删除规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="c4061-662">循环级联删除规则会在添加迁移时引发异常。</span><span class="sxs-lookup"><span data-stu-id="c4061-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="c4061-663">例如，如果 `Department.InstructorID` 属性定义为不可为 NULL：</span><span class="sxs-lookup"><span data-stu-id="c4061-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="c4061-664">EF Core 会配置级联删除规则，以在删除讲师时删除院系。</span><span class="sxs-lookup"><span data-stu-id="c4061-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="c4061-665">在删除讲师时删除院系并不是预期行为。</span><span class="sxs-lookup"><span data-stu-id="c4061-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="c4061-666">以下 [fluent API](#fluent-api-alternative-to-attributes) 将设置限制规则而不是级联规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="c4061-667">上面的代码会针对“系-讲师”关系禁用级联删除。</span><span class="sxs-lookup"><span data-stu-id="c4061-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="c4061-668">更新 Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-668">Update the Enrollment entity</span></span>

<span data-ttu-id="c4061-669">一份注册记录面向一名学生所注册的一门课程。</span><span class="sxs-lookup"><span data-stu-id="c4061-669">An enrollment record is for one course taken by one student.</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="c4061-671">用以下代码更新 *Models/Enrollment.cs* ：</span><span class="sxs-lookup"><span data-stu-id="c4061-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="c4061-672">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="c4061-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="c4061-673">FK 属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="c4061-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="c4061-674">注册记录面向一门课程，因此存在 `CourseID` FK 属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="c4061-675">一份注册记录针对一名学生，因此存在 `StudentID` FK 属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="c4061-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="c4061-676">多对多关系</span><span class="sxs-lookup"><span data-stu-id="c4061-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="c4061-677">`Student` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="c4061-678">`Enrollment` 实体充当数据库中“具有有效负载”的多对多联接表。</span><span class="sxs-lookup"><span data-stu-id="c4061-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="c4061-679">“具有有效负载”表示 `Enrollment` 表除了联接表的 FK 外还包含其他数据（本教程中为 PK 和 `Grade`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="c4061-680">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="c4061-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="c4061-681">（此关系图是使用适用于 EF 6.X 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 生成的。</span><span class="sxs-lookup"><span data-stu-id="c4061-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="c4061-682">本教程不介绍如何创建此关系图。）</span><span class="sxs-lookup"><span data-stu-id="c4061-682">Creating the diagram isn't part of the tutorial.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="c4061-684">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="c4061-685">如果 `Enrollment` 表不包含年级信息，则它只需包含两个 FK（`CourseID` 和 `StudentID`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="c4061-686">无有效负载的多对多联接表有时称为纯联接表 (PJT)。</span><span class="sxs-lookup"><span data-stu-id="c4061-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="c4061-687">`Instructor` 和 `Course` 实体具有使用纯联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="c4061-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="c4061-688">注意：EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="c4061-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="c4061-689">有关详细信息，请参阅 [EF Core 2.0 中的多对多关系](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/)。</span><span class="sxs-lookup"><span data-stu-id="c4061-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="c4061-690">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="c4061-690">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="c4061-692">用以下代码创建 Models/CourseAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="c4061-693">讲师-课程</span><span class="sxs-lookup"><span data-stu-id="c4061-693">Instructor-to-Courses</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="c4061-695">讲师-课程的多对多关系：</span><span class="sxs-lookup"><span data-stu-id="c4061-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="c4061-696">要求必须用实体集表示联接表。</span><span class="sxs-lookup"><span data-stu-id="c4061-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="c4061-697">为纯联接表（无有效负载的表）。</span><span class="sxs-lookup"><span data-stu-id="c4061-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="c4061-698">常规做法是将联接实体命名为 `EntityName1EntityName2`。</span><span class="sxs-lookup"><span data-stu-id="c4061-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="c4061-699">例如，使用此模式的“讲师-课程”联接表是 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="c4061-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="c4061-700">但是，我们建议使用可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="c4061-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="c4061-701">数据模型开始时很简单，其内容会逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="c4061-701">Data models start out simple and grow.</span></span> <span data-ttu-id="c4061-702">无有效负载联接 (PJT) 通常会发展为包含有效负载。</span><span class="sxs-lookup"><span data-stu-id="c4061-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="c4061-703">该名称以描述性实体名称开始，因此不需要随联接表更改而更改。</span><span class="sxs-lookup"><span data-stu-id="c4061-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="c4061-704">理想情况下，联接实体在业务领域中可能具有专业名称（可能是一个词）。</span><span class="sxs-lookup"><span data-stu-id="c4061-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="c4061-705">例如，可以使用名为“阅读率”的联接实体链接“书籍”和“读客”。</span><span class="sxs-lookup"><span data-stu-id="c4061-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="c4061-706">对于“讲师-课程”的多对多关系，使用 `CourseAssignment` 比使用`CourseInstructor`更合适。</span><span class="sxs-lookup"><span data-stu-id="c4061-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="c4061-707">组合键</span><span class="sxs-lookup"><span data-stu-id="c4061-707">Composite key</span></span>

<span data-ttu-id="c4061-708">FK 不能为 NULL。</span><span class="sxs-lookup"><span data-stu-id="c4061-708">FKs are not nullable.</span></span> <span data-ttu-id="c4061-709">`CourseAssignment` 中的两个 FK（`InstructorID` 和 `CourseID`）共同唯一标识 `CourseAssignment` 表的每一行。</span><span class="sxs-lookup"><span data-stu-id="c4061-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="c4061-710">`CourseAssignment` 不需要专用的 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="c4061-711">`InstructorID` 和 `CourseID` 属性充当组合 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="c4061-712">使用 Fluent API 是向 EF Core 指定组合 PK 的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="c4061-713">下一部分演示如何配置组合 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="c4061-714">组合键可确保：</span><span class="sxs-lookup"><span data-stu-id="c4061-714">The composite key ensures:</span></span>

* <span data-ttu-id="c4061-715">允许一门课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="c4061-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="c4061-716">允许一名讲师对应多行。</span><span class="sxs-lookup"><span data-stu-id="c4061-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="c4061-717">不允许相同的讲师和课程对应多行。</span><span class="sxs-lookup"><span data-stu-id="c4061-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="c4061-718">`Enrollment` 联接实体定义其自己的 PK，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="c4061-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="c4061-719">若要防止此类重复：</span><span class="sxs-lookup"><span data-stu-id="c4061-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="c4061-720">请在 FK 字段上添加唯一索引，或</span><span class="sxs-lookup"><span data-stu-id="c4061-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="c4061-721">配置具有主要组合键（与 `CourseAssignment` 类似）的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="c4061-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="c4061-722">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="c4061-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="c4061-723">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c4061-723">Update the DB context</span></span>

<span data-ttu-id="c4061-724">将以下突出显示的代码添加到 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="c4061-724">Add the following highlighted code to *Data/SchoolContext.cs* :</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="c4061-725">上面的代码添加新实体并配置 `CourseAssignment` 实体的组合 PK。</span><span class="sxs-lookup"><span data-stu-id="c4061-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="c4061-726">用 Fluent API 替代特性</span><span class="sxs-lookup"><span data-stu-id="c4061-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="c4061-727">上面代码中的 `OnModelCreating` 方法使用 Fluent API 配置 EF Core 行为。</span><span class="sxs-lookup"><span data-stu-id="c4061-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="c4061-728">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用。</span><span class="sxs-lookup"><span data-stu-id="c4061-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="c4061-729">[下面的代码](/ef/core/modeling/#use-fluent-api-to-configure-a-model)是 Fluent API 的示例：</span><span class="sxs-lookup"><span data-stu-id="c4061-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="c4061-730">在本教程中，Fluent API 仅用于不能通过特性完成的数据库映射。</span><span class="sxs-lookup"><span data-stu-id="c4061-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="c4061-731">但是，Fluent API 可以指定可通过特性完成的大多数格式设置、验证和映射规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="c4061-732">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="c4061-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="c4061-733">`MinimumLength` 不会更改架构，它仅应用最小长度验证规则。</span><span class="sxs-lookup"><span data-stu-id="c4061-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="c4061-734">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="c4061-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="c4061-735">特性和 Fluent API 可以相互混合。</span><span class="sxs-lookup"><span data-stu-id="c4061-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="c4061-736">某些配置只能通过 Fluent API 完成（指定组合 PK）。</span><span class="sxs-lookup"><span data-stu-id="c4061-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="c4061-737">有些配置只能通过特性完成 (`MinimumLength`)。</span><span class="sxs-lookup"><span data-stu-id="c4061-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="c4061-738">使用 Fluent API 或特性的建议做法：</span><span class="sxs-lookup"><span data-stu-id="c4061-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="c4061-739">选择以下两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="c4061-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="c4061-740">尽可能以前后一致的方法使用所选的方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="c4061-741">本教程中使用的某些特性可用于：</span><span class="sxs-lookup"><span data-stu-id="c4061-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="c4061-742">仅限验证（例如，`MinimumLength`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="c4061-743">仅限 EF Core 配置（例如，`HasKey`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="c4061-744">验证和 EF Core 配置（例如，`[StringLength(50)]`）。</span><span class="sxs-lookup"><span data-stu-id="c4061-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="c4061-745">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="c4061-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="c4061-746">显示关系的实体关系图</span><span class="sxs-lookup"><span data-stu-id="c4061-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="c4061-747">下图显示 EF Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="c4061-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="c4061-749">上面的关系图显示：</span><span class="sxs-lookup"><span data-stu-id="c4061-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="c4061-750">几条一对多关系线（1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="c4061-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="c4061-751">`Instructor` 和 `OfficeAssignment` 实体之间的一对零或一关系线（1 到 0..1）。</span><span class="sxs-lookup"><span data-stu-id="c4061-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="c4061-752">`Instructor` 和 `Department` 实体之间的零或一到多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="c4061-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="c4061-753">使用测试数据为数据库设定种子</span><span class="sxs-lookup"><span data-stu-id="c4061-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="c4061-754">更新 Data/DbInitializer.cs 中的代码：</span><span class="sxs-lookup"><span data-stu-id="c4061-754">Update the code in *Data/DbInitializer.cs* :</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="c4061-755">前面的代码为新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="c4061-756">大多数此类代码会创建新实体对象并加载示例数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="c4061-757">示例数据用于测试。</span><span class="sxs-lookup"><span data-stu-id="c4061-757">The sample data is used for testing.</span></span> <span data-ttu-id="c4061-758">有关如何对多对多联接表进行种子设定的示例，请参阅 `Enrollments` 和 `CourseAssignments`。</span><span class="sxs-lookup"><span data-stu-id="c4061-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="c4061-759">添加迁移</span><span class="sxs-lookup"><span data-stu-id="c4061-759">Add a migration</span></span>

<span data-ttu-id="c4061-760">生成项目。</span><span class="sxs-lookup"><span data-stu-id="c4061-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="c4061-763">前面的命令显示可能存在数据丢失的相关警告。</span><span class="sxs-lookup"><span data-stu-id="c4061-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="c4061-764">如果运行 `database update` 命令，则会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="c4061-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="c4061-765">应用迁移</span><span class="sxs-lookup"><span data-stu-id="c4061-765">Apply the migration</span></span>

<span data-ttu-id="c4061-766">现已有一个数据库，需要考虑如何将未来的更改应用到其中。</span><span class="sxs-lookup"><span data-stu-id="c4061-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="c4061-767">本教程演示两种方法：</span><span class="sxs-lookup"><span data-stu-id="c4061-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="c4061-768">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="c4061-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="c4061-769">[将迁移应用到现有数据库](#applyexisting)。</span><span class="sxs-lookup"><span data-stu-id="c4061-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="c4061-770">虽然此方法更复杂且耗时，但在实际应用和生产环境中为首选方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="c4061-771">**说明** ：这是本教程的一个可选部分。</span><span class="sxs-lookup"><span data-stu-id="c4061-771">**Note** : This is an optional section of the tutorial.</span></span> <span data-ttu-id="c4061-772">你可以执行删除和重新创建的相关步骤并跳过此部分。</span><span class="sxs-lookup"><span data-stu-id="c4061-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="c4061-773">如果希望执行本部分中的步骤，请勿执行删除和重新创建步骤。</span><span class="sxs-lookup"><span data-stu-id="c4061-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c4061-774">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="c4061-774">Drop and re-create the database</span></span>

<span data-ttu-id="c4061-775">已更新 `DbInitializer` 中的代码将为新实体添加种子数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="c4061-776">若要强制 EF Core 创建新的 DB，请删除并更新 DB：</span><span class="sxs-lookup"><span data-stu-id="c4061-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c4061-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4061-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c4061-778">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c4061-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="c4061-779">从 PMC 运行 `Get-Help about_EntityFrameworkCore`，获取帮助信息。</span><span class="sxs-lookup"><span data-stu-id="c4061-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c4061-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c4061-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c4061-781">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="c4061-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="c4061-782">项目文件夹包含 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="c4061-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="c4061-783">在命令窗口中输入以下内容：</span><span class="sxs-lookup"><span data-stu-id="c4061-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="c4061-784">运行应用。</span><span class="sxs-lookup"><span data-stu-id="c4061-784">Run the app.</span></span> <span data-ttu-id="c4061-785">运行应用后将运行 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="c4061-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="c4061-786">`DbInitializer.Initialize` 将填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="c4061-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="c4061-787">在 SSOX 中打开数据库：</span><span class="sxs-lookup"><span data-stu-id="c4061-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="c4061-788">如果之前已打开过 SSOX，请单击“刷新”按钮。</span><span class="sxs-lookup"><span data-stu-id="c4061-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="c4061-789">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="c4061-789">Expand the **Tables** node.</span></span> <span data-ttu-id="c4061-790">随后将显示出已创建的表。</span><span class="sxs-lookup"><span data-stu-id="c4061-790">The created tables are displayed.</span></span>

![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="c4061-792">查看 CourseAssignment 表：</span><span class="sxs-lookup"><span data-stu-id="c4061-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="c4061-793">右键单击 CourseAssignment 表，然后选择“查看数据” 。</span><span class="sxs-lookup"><span data-stu-id="c4061-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="c4061-794">验证 CourseAssignment 表包含数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-794">Verify the **CourseAssignment** table contains data.</span></span>

![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="c4061-796">将迁移应用到现有数据库</span><span class="sxs-lookup"><span data-stu-id="c4061-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="c4061-797">本部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="c4061-797">This section is optional.</span></span> <span data-ttu-id="c4061-798">只有当跳过之前的[删除并重新创建数据库](#drop)部分时才可以执行上述步骤。</span><span class="sxs-lookup"><span data-stu-id="c4061-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="c4061-799">当现有数据与迁移一起运行时，可能存在不满足现有数据的 FK 约束。</span><span class="sxs-lookup"><span data-stu-id="c4061-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="c4061-800">使用生产数据时，必须采取步骤来迁移现有数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="c4061-801">本部分提供修复 FK 约束冲突的示例。</span><span class="sxs-lookup"><span data-stu-id="c4061-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="c4061-802">务必在备份后执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="c4061-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="c4061-803">如果已完成上述部分并更新数据库，则不要执行这些代码更改。</span><span class="sxs-lookup"><span data-stu-id="c4061-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="c4061-804">{timestamp}_ComplexDataModel.cs 文件包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="c4061-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="c4061-805">上面的代码将向 `Course` 表添加不可为 NULL 的 `DepartmentID` FK。</span><span class="sxs-lookup"><span data-stu-id="c4061-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="c4061-806">前面教程中的数据库在 `Course` 中包含行，以便迁移时不会更新表。</span><span class="sxs-lookup"><span data-stu-id="c4061-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="c4061-807">若要使 `ComplexDataModel` 迁移可与现有数据搭配运行：</span><span class="sxs-lookup"><span data-stu-id="c4061-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="c4061-808">请更改代码以便为新列 (`DepartmentID`) 赋予默认值。</span><span class="sxs-lookup"><span data-stu-id="c4061-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="c4061-809">创建名为“临时”的虚拟系来充当默认的系。</span><span class="sxs-lookup"><span data-stu-id="c4061-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="c4061-810">修复外键约束</span><span class="sxs-lookup"><span data-stu-id="c4061-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="c4061-811">更新 `ComplexDataModel` 类 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="c4061-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="c4061-812">打开 {timestamp}_ComplexDataModel.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="c4061-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="c4061-813">对将 `DepartmentID` 列添加到 `Course` 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="c4061-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="c4061-814">添加以下突出显示的代码。</span><span class="sxs-lookup"><span data-stu-id="c4061-814">Add the following highlighted code.</span></span> <span data-ttu-id="c4061-815">新代码在 `.CreateTable( name: "Department"` 块后：</span><span class="sxs-lookup"><span data-stu-id="c4061-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="c4061-816">经过上面的更改，现有的 `Course` 行将在 `ComplexDataModel` `Up` 方法运行后与“临时”院系建立联系。</span><span class="sxs-lookup"><span data-stu-id="c4061-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="c4061-817">生产应用可能：</span><span class="sxs-lookup"><span data-stu-id="c4061-817">A production app would:</span></span>

* <span data-ttu-id="c4061-818">包含用于将 `Department` 行和相关 `Course` 行添加到新 `Department` 行的代码或脚本。</span><span class="sxs-lookup"><span data-stu-id="c4061-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="c4061-819">不会使用“临时”系或 `Course.DepartmentID` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="c4061-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="c4061-820">下一教程将介绍相关数据。</span><span class="sxs-lookup"><span data-stu-id="c4061-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4061-821">其他资源</span><span class="sxs-lookup"><span data-stu-id="c4061-821">Additional resources</span></span>

* [<span data-ttu-id="c4061-822">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="c4061-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="c4061-823">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="c4061-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="c4061-824">[上一页](xref:data/ef-rp/migrations)
> [下一页](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="c4061-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
