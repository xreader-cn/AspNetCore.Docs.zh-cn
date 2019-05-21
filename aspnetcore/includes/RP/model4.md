<a name="codegenerator"></a> 下表详细说明了 ASP.NET Core 代码生成器参数：

| 参数               | 说明|
| ----------------- | ------------ |
| -m  | 模型的名称。 |
| -dc  | 要使用的 `DbContext` 类。 |
| -udl | 使用默认布局。 |
| -outDir | 用于创建视图的相对输出文件夹路径。 |
| --referenceScriptLibraries | 向“编辑”和“创建”页面添加 `_ValidationScriptsPartial` |

使用 `h` 开关获取 `aspnet-codegenerator razorpage` 命令方面的帮助：

```console
dotnet aspnet-codegenerator razorpage -h
```
