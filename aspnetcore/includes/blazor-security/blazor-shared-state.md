Blazor 服务器应用位于服务器内存中。 这意味着同一进程中托管了多个应用。 对于每个应用会话，Blazor 会启动具有其自己的 DI 容器作用域的线路。 这意味着，每个 Blazor 会话的作用域内服务都是唯一的。

> [!WARNING]
> 我们不建议同一服务器上的应用共享使用单一实例服务的状态，除非采取了极其谨慎的措施，因为这可能会带来安全漏洞，如跨线路泄露用户状态。

如果有状态的单一实例服务是专门为 Blazor 应用设计的，则可以在该应用中使用这些服务。 例如，假设用户无法控制使用哪些缓存密钥，则可以将内存缓存用作单一实例，因为它需要一个密钥来访问给定的条目。

**另外，出于安全原因，不得在 Blazor 应用中使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>。** Blazor 应用在 ASP.NET Core 管道的上下文之外运行。 <xref:Microsoft.AspNetCore.Http.HttpContext> 既不保证在 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 中可用，也不保证它会保留启动了 Blazor 应用的上下文。

若要向 Blazor 应用传递请求状态，建议在初次呈现应用时通过传递到根组件的参数进行传递：

* 使用要传递到 Blazor 应用的所有数据定义类。
* 使用目前可用的 <xref:Microsoft.AspNetCore.Http.HttpContext> 在 Razor 页中填充该数据。
* 将数据作为传递给根组件（应用）的参数传递给 Blazor 应用。
* 在根组件中定义一个参数，用于保存即将传递给应用的数据。
* 在应用中使用用户特定的数据；或者，将该数据复制到 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中的作用域内的服务，以便可以跨应用使用该数据。

有关更多信息及代码示例，请参见 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。
