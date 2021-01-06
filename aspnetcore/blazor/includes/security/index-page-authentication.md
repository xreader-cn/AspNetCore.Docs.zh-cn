<span data-ttu-id="0be17-101">索引页 (`wwwroot/index.html`) 包含一个脚本，用于在 JavaScript 中定义 `AuthenticationService`。</span><span class="sxs-lookup"><span data-stu-id="0be17-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="0be17-102">`AuthenticationService` 处理 OIDC 协议的低级别详细信息。</span><span class="sxs-lookup"><span data-stu-id="0be17-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="0be17-103">应用从内部调用脚本中定义的方法以执行身份验证操作。</span><span class="sxs-lookup"><span data-stu-id="0be17-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
