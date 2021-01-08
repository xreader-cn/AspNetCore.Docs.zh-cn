<span data-ttu-id="37b1a-101">读取相关配置值的首选方法是使用[选项模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="37b1a-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="37b1a-102">例如，若要读取以下配置值，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="37b1a-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="37b1a-103">创建以下 `PositionOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="37b1a-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="37b1a-104">选项类：</span><span class="sxs-lookup"><span data-stu-id="37b1a-104">An options class:</span></span>

* <span data-ttu-id="37b1a-105">必须是包含公共无参数构造函数的非抽象类。</span><span class="sxs-lookup"><span data-stu-id="37b1a-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="37b1a-106">类型的所有公共读写属性都已绑定。</span><span class="sxs-lookup"><span data-stu-id="37b1a-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="37b1a-107">字段不是绑定的。</span><span class="sxs-lookup"><span data-stu-id="37b1a-107">Fields are \***not** _ bound.</span></span> <span data-ttu-id="37b1a-108">在上面的代码中，`Position` 未绑定。</span><span class="sxs-lookup"><span data-stu-id="37b1a-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="37b1a-109">由于使用了 `Position` 属性，因此在将类绑定到配置提供程序时，不需要在应用中对字符串 `"Position"` 进行硬编码。</span><span class="sxs-lookup"><span data-stu-id="37b1a-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="37b1a-110">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="37b1a-110">The following code:</span></span>

<span data-ttu-id="37b1a-111">_ 调用 [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) 将 `PositionOptions` 类绑定到 `Position` 部分。</span><span class="sxs-lookup"><span data-stu-id="37b1a-111">_ Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="37b1a-112">显示 `Position` 配置数据。</span><span class="sxs-lookup"><span data-stu-id="37b1a-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="37b1a-113">在上面的代码中，默认读取在应用启动后对 JSON 配置文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="37b1a-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="37b1a-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定的类型。</span><span class="sxs-lookup"><span data-stu-id="37b1a-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="37b1a-115">使用 `ConfigurationBinder.Get<T>` 可能比使用 `ConfigurationBinder.Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="37b1a-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="37b1a-116">下面的代码演示如何将 `ConfigurationBinder.Get<T>` 与 `PositionOptions` 类配合使用：</span><span class="sxs-lookup"><span data-stu-id="37b1a-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="37b1a-117">在上面的代码中，默认读取在应用启动后对 JSON 配置文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="37b1a-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="37b1a-118">使用选项模式时的替代方法是绑定 `Position` 部分并将它添加到[依赖项注入服务容器](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="37b1a-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="37b1a-119">在以下代码中，`PositionOptions` 已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> 被添加到了服务容器并已绑定到了配置：</span><span class="sxs-lookup"><span data-stu-id="37b1a-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="37b1a-120">通过使用前面的代码，以下代码将读取位置选项：</span><span class="sxs-lookup"><span data-stu-id="37b1a-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="37b1a-121">在上面的代码中，不会读取在应用启动后对 JSON 配置文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="37b1a-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="37b1a-122">若要读取在应用启动后的更改，请使用 [IOptionsSnapshot](xref:fundamentals/configuration/options#ios)。</span><span class="sxs-lookup"><span data-stu-id="37b1a-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
