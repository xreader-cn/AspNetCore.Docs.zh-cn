<!-- THIS INCLUDE USED BY MVC AND RP -->
向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性指定数据的类型（日期）。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 会在后续教程中介绍。
