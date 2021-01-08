读取相关配置值的首选方法是使用[选项模式](xref:fundamentals/configuration/options)。 例如，若要读取以下配置值，请执行以下操作：

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

创建以下 `PositionOptions` 类：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

选项类：

* 必须是包含公共无参数构造函数的非抽象类。
* 类型的所有公共读写属性都已绑定。
* 字段不是绑定的。 在上面的代码中，`Position` 未绑定。 由于使用了 `Position` 属性，因此在将类绑定到配置提供程序时，不需要在应用中对字符串 `"Position"` 进行硬编码。

下面的代码：

_ 调用 [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) 将 `PositionOptions` 类绑定到 `Position` 部分。
* 显示 `Position` 配置数据。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

在上面的代码中，默认读取在应用启动后对 JSON 配置文件所做的更改。

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定的类型。 使用 `ConfigurationBinder.Get<T>` 可能比使用 `ConfigurationBinder.Bind` 更方便。 下面的代码演示如何将 `ConfigurationBinder.Get<T>` 与 `PositionOptions` 类配合使用：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

在上面的代码中，默认读取在应用启动后对 JSON 配置文件所做的更改。

使用选项模式时的替代方法是绑定 `Position` 部分并将它添加到[依赖项注入服务容器](xref:fundamentals/dependency-injection)。 在以下代码中，`PositionOptions` 已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> 被添加到了服务容器并已绑定到了配置：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

通过使用前面的代码，以下代码将读取位置选项：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

在上面的代码中，不会读取在应用启动后对 JSON 配置文件所做的更改。 若要读取在应用启动后的更改，请使用 [IOptionsSnapshot](xref:fundamentals/configuration/options#ios)。
