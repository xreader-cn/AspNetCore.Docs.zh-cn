<a name="csc"></a>

## <a name="combining-service-collection"></a>合并服务集合

请考虑以下包含几个服务集合的 `ConfigureServices`：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

可以将相关的注册组移动到扩展方法以注册服务。 例如，配置服务会被添加到以下类中：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

剩余的服务会在类似的类中注册。 下面的 `ConfigureServices` 使用新扩展方法来注册服务：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***注意：*** 每个 `services.Add{SERVICE_NAME}` 扩展方法添加并可能配置服务。 例如，<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> 会添加带视图的 MVC 控制器所需的服务。 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 会添加 Razor Pages 所需的服务。 我们建议应用遵循此命名约定。 将扩展方法置于 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空间中以封装服务注册的组。