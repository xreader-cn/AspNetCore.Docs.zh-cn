## <a name="share-interop-code-in-a-class-library"></a>在类库中共享互操作代码

可在类库中包含 JS 互操作代码，以便能在 NuGet 包中共享代码。

类库会处理在生成的程序集中嵌入 JavaScript 资源的操作。 JavaScript 文件位于 wwwroot 文件夹中  。 工具负责在生成库时嵌入资源。

按引用任何其他 NuGet 包的方式在应用的项目文件中引用生成的 NuGet 包。 包还原后，应用代码可如同是 C# 一样调入 JavaScript。

有关更多信息，请参见 <xref:blazor/components/class-libraries>。
