<span data-ttu-id="91509-101">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="91509-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="91509-102">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="91509-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="91509-103">数据库需要 `Id` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="91509-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="91509-104">`[DataType(DataType.Date)]`：[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="91509-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="91509-105">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="91509-105">With this attribute:</span></span>

  * <span data-ttu-id="91509-106">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="91509-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="91509-107">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="91509-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="91509-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="91509-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>