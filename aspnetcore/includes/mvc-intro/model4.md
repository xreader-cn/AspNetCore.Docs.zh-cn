下表详细说明了 ASP.NET Core 代码生成器参数：

| 参数               | 说明|
| ----------------- | ------------ |
| -m  | 模型的名称。 |
| -dc  | 数据上下文。 |
| -udl | 使用默认布局。 |
| --relativeFolderPath | 用于创建视图的相对输出文件夹路径。 |
| --useDefaultLayout | 应为视图使用默认布局。 |
| --referenceScriptLibraries | 向“编辑”和“创建”页面添加 `_ValidationScriptsPartial` |

使用 `h` 开关获取 `aspnet-codegenerator controller` 命令方面的帮助：

```console
dotnet aspnet-codegenerator controller -h
```