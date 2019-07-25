<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>将验证规则添加到电影模型

DataAnnotations 命名空间提供一组内置验证特性，可通过声明方式应用于类或属性。 DataAnnotations 还包含 `DataType` 等格式特性，有助于格式设置但不提供任何验证。

更新 `Movie` 类以使用内置的 `Required`、`StringLength`、`RegularExpression` 和 `Range` 验证特性。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

验证特性指定要对应用这些特性的模型属性强制执行的行为：

* `Required` 和 `MinimumLength` 特性表示属性必须有值；但用户可输入空格来满足此验证。
* `RegularExpression` 特性用于限制可输入的字符。 在上述代码中，即“Genre”（分类）：

  * 只能使用字母。
  * 第一个字母必须为大写。 不允许使用空格、数字和特殊字符。

* `RegularExpression`“Rating”（分级）：

  * 要求第一个字符为大写字母。
  * 允许在后续空格中使用特殊字符和数字。 “PG-13”对“分级”有效，但对于“分类”无效。

* `Range` 特性将值限制在指定范围内。
* `StringLength` 特性使你能够设置字符串属性的最大长度，以及可选的最小长度。
* 从本质上来说，需要值类型（如 `decimal`、`int`、`float`、`DateTime`），但不需要 `[Required]` 特性。

让 ASP.NET Core 强制自动执行验证规则有助于提升你的应用的可靠性。 同时它能确保你无法忘记验证某些内容，并防止你无意中将错误数据导入数据库。
