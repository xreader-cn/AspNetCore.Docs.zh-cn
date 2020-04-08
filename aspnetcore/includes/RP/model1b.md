<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="cbfc1-101">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="cbfc1-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="cbfc1-102">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="cbfc1-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="cbfc1-103">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="cbfc1-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="cbfc1-104">`[DataType(DataType.Date)]`：[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性指定数据的类型（日期）。</span><span class="sxs-lookup"><span data-stu-id="cbfc1-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="cbfc1-105">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="cbfc1-105">With this attribute:</span></span>

  * <span data-ttu-id="cbfc1-106">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="cbfc1-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="cbfc1-107">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="cbfc1-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="cbfc1-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="cbfc1-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
