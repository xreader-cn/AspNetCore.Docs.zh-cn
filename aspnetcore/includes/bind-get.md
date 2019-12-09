> [!WARNING]
> 出于安全原因，必须选择绑定 `GET` 请求数据以对模型属性进行分页。 请在将用户输入映射到属性前对其进行验证。 当处理依赖查询字符串或路由值的方案时，选择加入 `GET` 绑定非常有用。
>
> 若要将属性绑定在 `GET` 请求上，请将 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 特性的 `SupportsGet` 属性设置为 `true`：
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> 有关详细信息，请参阅 [ASP.NET Core Community Standup:Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s)（绑定 GET 讨论）。
