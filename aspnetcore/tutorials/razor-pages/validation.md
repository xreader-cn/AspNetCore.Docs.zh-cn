---
title: 第 8 部分：添加验证
author: rick-anderson
description: Razor 页面教程系列的第 8 部分。
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f155922c9cb5ea7fdbad0963221ceddd19f4fe60
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419949"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a>Razor 页面教程系列的第 8 部分。

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本部分中向 `Movie` 模型添加了验证逻辑。 每当用户创建或编辑电影时，都会强制执行验证规则。

## <a name="validation"></a>验证

软件开发的一个关键原则被称为 [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself)（即“不要自我重复”）  。 Razor 页面鼓励进行仅指定一次功能的开发，且功能在整个应用中反映。 DRY 可以帮助：

* 减少应用中的代码量。
* 使代码更加不易出错，且更易于测试和维护。

Razor 页面和实体框架提供的验证支持是 DRY 原则的极佳示例：

* 验证规则在模型类中的某处以声明方式指定。
* 规则在应用的所有位置强制执行。

## <a name="add-validation-rules-to-the-movie-model"></a>将验证规则添加到电影模型

`DataAnnotations` 命名空间提供以下内容：

* 一组内置验证特性，可通过声明方式应用于类或属性。
* `[DataType]` 等格式特性，这些特性可帮助进行格式设置，但不提供任何验证。

更新 `Movie` 类以使用内置的 `[Required]`、`[StringLength]`、`[RegularExpression]` 和 `[Range]` 验证特性。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

验证特性指定要对应用这些特性的模型属性强制执行的行为：

* `[Required]` 和 `[MinimumLength]` 特性指示属性必须具有一个值。 不阻止用户输入空格来满足此验证。
* `[RegularExpression]` 特性用于限制可输入的字符。 在上述代码中，`Genre`：

  * 只能使用字母。
  * 第一个字母必须为大写。 不允许使用空格、数字和特殊字符。

* `RegularExpression` `Rating`：

  * 要求第一个字符为大写字母。
  * 允许在后续空格中使用特殊字符和数字。 “PG-13”对“分级”有效，但对于“`Genre`”无效。

* `[Range]` 特性将值限制在指定的范围内。
* `[StringLength]` 特性可以设置字符串属性的最大长度，以及可选的最小长度。
* 从本质上来说，需要值类型（如 `decimal`、`int`、`float`、`DateTime`），但不需要 `[Required]` 特性。

上述验证规则用于进行演示，它们并不是适用于生产系统的最佳规则。 例如，前面的规则会阻止用户仅使用两个字符输入一部电影，并且不允许 `Genre` 中有特殊字符。

让 ASP.NET Core 强制自动执行验证规则有助于：

* 提升应用的可靠性。
* 减少将无效数据保存到数据库的几率。

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Razor 页面中的验证错误 UI

运行应用并导航到“页面/电影”。

选择“新建”链接。 使用无效值填写表单。 当 jQuery 客户端验证检测到错误时，会显示一条错误消息。

![带有多个 jQuery 客户端验证错误的电影视图表单](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

请注意表单如何自动呈现每个包含无效值的字段中的验证错误消息。 客户端（使用 JavaScript 和 jQuery）和服务器端（若用户禁用 JavaScript）都必定会遇到这些错误。

一项重要优势是，无需在“创建”或“编辑”页面中更改代码。 在模型应用数据注释后，即已启用验证 UI。 本教程中创建的 Razor 页面自动选取了验证规则（使用 `Movie` 模型类的属性上的验证特性）。 使用“编辑”页面测试验证后，即已应用相同验证。

存在客户端验证错误时，不会将表单数据发布到服务器。 请通过以下一种或多种方法验证是否未发布表单数据：

* 在 `OnPostAsync` 方法中放置一个断点。 通过选择“创建”或“保存”来提交表单 。 从未命中断点。
* 使用 [Fiddler 工具](https://www.telerik.com/fiddler)。
* 使用浏览器开发人员工具监视网络流量。

### <a name="server-side-validation"></a>服务器端验证

在浏览器中禁用 JavaScript 后，提交出错表单将发布到服务器。

（可选）测试服务器端验证：

1. 在浏览器中禁用 JavaScript。 可以使用浏览器的开发人员工具禁用 JavaScript。 如果无法在此浏览器中禁用 JavaScript，请尝试其他浏览器。
1. 在“创建”或“编辑”页面的 `OnPostAsync` 方法中设置断点。
1. 提交包含无效数据的表单。
1. 验证模型状态是否无效：

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
或者可以[禁用服务器上的客户端验证](xref:mvc/models/validation#disable-client-side-validation)。

以下代码显示了之前在本教程中设定其基架的“Create.cshtml”的一部分。 “创建”和“编辑”页面使用它来实现以下操作：

* 显示初始表单。
* 在出现错误时重现显示此表单。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[输入标记帮助程序](xref:mvc/views/working-with-forms)使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 特性并在客户端生成 jQuery 验证所需的 HTML 特性。 [验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)用于显示验证错误。 有关详细信息，请参阅[验证](xref:mvc/models/validation)。

“创建”和“编辑”页面中没有验证规则。 仅可在 `Movie` 类中指定验证规则和错误字符串。 这些验证规则将自动应用于编辑 `Movie` 模型的 Razor 页面。

需要更改验证逻辑时，也只能在该模型中更改。 将始终在整个应用程序中应用验证（在一处定义验证逻辑）。 单处验证有助于保持代码干净，且更易于维护和更新。

## <a name="use-datatype-attributes"></a>使用 DataType 特性

检查 `Movie` 类。 除了一组内置的验证特性，`System.ComponentModel.DataAnnotations` 命名空间还提供格式特性。 `[DataType]` 特性应用于 `ReleaseDate` 和 `Price` 属性。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`[DataType]` 特性提供以下内容：

* 提供提示，供视图引擎设置数据的格式。
* 提供属性，例如 URL 的 `<a>` 以及电子邮件的 `<a href="mailto:EmailAddress.com">`。

使用 `[RegularExpression]` 特性验证数据的格式。 `[DataType]` 属性用于指定比数据库内部类型更具体的数据类型。 `[DataType]` 特性不是验证特性。 示例应用程序中仅显示日期，不显示时间。

`DataType` 枚举提供多种数据类型，如 `Date`、`Time`、`PhoneNumber`、`Currency`、`EmailAddress` 等。 

`[DataType]` 特性：

* 可以使应用程序自动提供类型特定的功能。 例如，可为 `DataType.EmailAddress` 创建 `mailto:` 链接。
* 可在支持 HTML5 的浏览器中提供 `DataType.Date` 日期选择器。
* 发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。
* 不提供任何验证。

`DataType.Date` 不指定显示日期的格式。 默认情况下，数据字段根据基于服务器的 `CultureInfo` 的默认格式进行显示。

要使 Entity Framework Core 能将 `Price` 正确地映射到数据库中的货币，则必须使用 `[Column(TypeName = "decimal(18, 2)")]` 数据注释。 有关详细信息，请参阅[数据类型](/ef/core/modeling/relational/data-types)。

`[DisplayFormat]` 特性用于显式指定日期格式：

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` 设置用于指定在显示值进行编辑时将应用格式。 某些字段可能不需要此行为。 例如，在货币值中，可能不希望编辑 UI 中存在货币符号。

可单独使用 `[DisplayFormat]` 特性，但通常建议使用 `[DataType]` 特性。 `[DataType]` 特性按照数据在屏幕上的呈现方式传达数据的语义。 `[DataType]` 特性可提供 `[DisplayFormat]` 所不具有的以下优点：

* 浏览器可启用 HTML5 功能（例如显示日历控件、区域设置适用的货币符号、电子邮件链接等）。
* 默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。
* 借助 `[DataType]` 特性，ASP.NET Core 框架可选择适当的字段模板来呈现数据。 单独使用时，`DisplayFormat` 特性将使用字符串模板。

**注意**：jQuery 验证不适用于 `[Range]` 特性和 `DateTime`。 例如，以下代码将始终显示客户端验证错误，即便日期在指定的范围内：

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

最佳做法是避免在模型中编译固定日期，因此不推荐使用 `[Range]` 特性和 `DateTime`。 使用[配置](xref:fundamentals/configuration/index)指定日期范围和其他经常会更改的值，而不是在代码中指定它们。

以下代码显示组合在一行上的特性：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Razor 页面和 EF Core 入门](xref:data/ef-rp/intro)显示了 Razor 页面的高级 EF Core 操作。

### <a name="apply-migrations"></a>应用迁移

应用于类的 DataAnnotations 会更改架构。 例如，应用于 `Title` 字段的 DataAnnotations 会：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* 将字符数限制为 60。
* 不允许使用 `null` 值。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

`Movie` 表当前具有以下架构：

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

前面的架构更改不会导致 EF 引发异常。 不过，请创建迁移，使架构与模型保持一致。

从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 
在 PMC 中，输入以下命令：

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database` 运行 `New_DataAnnotations` 类的 `Up` 方法。 检查 `Up` 方法：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

更新的 `Movie` 表具有以下架构：

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

SQLite 不需要迁移。

---

### <a name="publish-to-azure"></a>发布到 Azure

若要了解如何部署到 Azure，请参阅[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET Core 应用](/azure/app-service/tutorial-dotnetcore-sqldb-app)。

感谢读完这篇 Razor Pages 简介。 [Razor Pages 和 EF Core 入门](xref:data/ef-rp/intro)是本教程的优选后续教程。

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [上一篇：添加新字段](xref:tutorials/razor-pages/new-field)
