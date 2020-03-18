<!-- Options common to Razor Pages and Controller -->
| 选项               | 描述|
| ----------------- | ------------ |
| --model 或 -m  | 要使用的模型类。 |
| --dataContext 或 -dc  | 要使用的 `DbContext` 类。 |
| --bootstrapVersion 或 -b  | 指定启动版本。 有效值为 `3` 或 `4`。 默认值为 `4`。 如果需要一个 wwwroot  目录而当前不存在，则创建一个，其中包含指定版本的启动文件。 |
| --referenceScriptLibraries 或 -scripts |  在生成的视图中引用脚本库。 向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`。 |
| --layout 或 -l | 要使用的自定义布局页。 |
| --useDefaultLayout 或 -udl | 使用视图的默认布局。 |
| --force 或 -f | 覆盖现有文件。 |
| --relativeFolderPath 或 -outDir | 生成文件的项目的相对输出文件夹路径。 如果未指定，则会在项目文件夹中生成文件。 |