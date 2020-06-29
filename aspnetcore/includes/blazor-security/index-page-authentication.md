索引页 (`wwwroot/index.html`) 包含一个脚本，用于在 JavaScript 中定义 `AuthenticationService`。 `AuthenticationService` 处理 OIDC 协议的低级别详细信息。 应用从内部调用脚本中定义的方法以执行身份验证操作。

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
