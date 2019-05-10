---
title: ASP.NET Core 中的文件上传
author: ardalis
description: 如何在 ASP.NET Core MVC 中使用模型绑定和流式处理上传文件。
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 3db2e08acc1552957f28c7015f9a75af4a57fdcd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65084867"
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="fae35-103">ASP.NET Core 中的文件上传</span><span class="sxs-lookup"><span data-stu-id="fae35-103">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="fae35-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fae35-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fae35-105">ASP.NET MVC 操作支持使用简单的模型绑定（针对较小文件）或流式处理（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="fae35-105">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="fae35-106">查看或下载 GitHub 中的示例</span><span class="sxs-lookup"><span data-stu-id="fae35-106">View or download sample from GitHub</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="fae35-107">使用模型绑定上传小文件</span><span class="sxs-lookup"><span data-stu-id="fae35-107">Uploading small files with model binding</span></span>

<span data-ttu-id="fae35-108">要上传小文件，可使用多部分 HTML 窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="fae35-108">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="fae35-109">下方显示使用 Razor（支持上传多个文件）的示例窗体：</span><span class="sxs-lookup"><span data-stu-id="fae35-109">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple>
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload">
        </div>
    </div>
</form>
```

<span data-ttu-id="fae35-110">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="fae35-110">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="fae35-111">上面显示的 `files` 输入元素支持上传多个文件。</span><span class="sxs-lookup"><span data-stu-id="fae35-111">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="fae35-112">忽略此输入元素上的 `multiple` 属性，只允许上传单个文件。</span><span class="sxs-lookup"><span data-stu-id="fae35-112">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="fae35-113">上述标记在浏览器中呈现为：</span><span class="sxs-lookup"><span data-stu-id="fae35-113">The above markup renders in a browser as:</span></span>

![文件上传窗体](file-uploads/_static/upload-form.png)

<span data-ttu-id="fae35-115">上传到服务器的单个文件可使用 [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="fae35-115">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="fae35-116">`IFormFile` 具有以下结构：</span><span class="sxs-lookup"><span data-stu-id="fae35-116">`IFormFile` has the following structure:</span></span>

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> <span data-ttu-id="fae35-117">切勿依赖或信任未经验证的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="fae35-117">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="fae35-118">`FileName` 属性应仅用于显示目的。</span><span class="sxs-lookup"><span data-stu-id="fae35-118">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="fae35-119">使用模型绑定和 `IFormFile` 接口上传文件时，操作方法可接受单个 `IFormFile` 或代表多个文件的 `IEnumerable<IFormFile>`（或 `List<IFormFile>`）。</span><span class="sxs-lookup"><span data-stu-id="fae35-119">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="fae35-120">以下示例循环访问一个或多个上传的文件、将其保存到本地文件系统，并返回上传的文件总数和大小。</span><span class="sxs-lookup"><span data-stu-id="fae35-120">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

<span data-ttu-id="fae35-121">使用 `IFormFile` 技术上传的文件在处理之前会缓存在内存中或 Web 服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="fae35-121">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="fae35-122">在操作方法中，`IFormFile` 内容可作为流访问。</span><span class="sxs-lookup"><span data-stu-id="fae35-122">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="fae35-123">除了本地文件系统之外，还可将文件流式传输到 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)或[实体框架](/ef/core/index)。</span><span class="sxs-lookup"><span data-stu-id="fae35-123">In addition to the local file system, files can be streamed to [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) or [Entity Framework](/ef/core/index).</span></span>

<span data-ttu-id="fae35-124">要使用实体框架将二进制文件数据存储在数据库中，请在实体上定义类型为 `byte[]` 属性：</span><span class="sxs-lookup"><span data-stu-id="fae35-124">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="fae35-125">指定类型为 `IFormFile` 的 viewmodel 属性：</span><span class="sxs-lookup"><span data-stu-id="fae35-125">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="fae35-126">`IFormFile` 可直接用作操作方法参数或 viewmodel 属性，如上所示。</span><span class="sxs-lookup"><span data-stu-id="fae35-126">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="fae35-127">将 `IFormFile` 复制到流并将其保存到字节数组中：</span><span class="sxs-lookup"><span data-stu-id="fae35-127">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser 
        {
            UserName = model.Email,
            Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted

    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> <span data-ttu-id="fae35-128">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="fae35-128">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="fae35-129">使用流式处理上传大文件</span><span class="sxs-lookup"><span data-stu-id="fae35-129">Uploading large files with streaming</span></span>

<span data-ttu-id="fae35-130">如果文件上传的大小或频率会导致应用出现资源问题，请考虑使用流式处理上传文件，而不是像如上所示的模型绑定方法那样全部缓冲文件。</span><span class="sxs-lookup"><span data-stu-id="fae35-130">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="fae35-131">尽管使用 `IFormFile` 和模型绑定是更为简单的一种解决方案，但流式处理需要大量步骤才能正确实现。</span><span class="sxs-lookup"><span data-stu-id="fae35-131">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="fae35-132">任何超过 64KB 的单个缓冲文件会从 RAM 移动到服务器磁盘上的临时文件中。</span><span class="sxs-lookup"><span data-stu-id="fae35-132">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="fae35-133">文件上传所用的资源（磁盘、RAM）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="fae35-133">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="fae35-134">流式处理与性能没有太大的关系，而是与规模有关。</span><span class="sxs-lookup"><span data-stu-id="fae35-134">Streaming isn't so much about perf, it's about scale.</span></span> <span data-ttu-id="fae35-135">如果尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="fae35-135">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="fae35-136">以下示例演示如何通过 JavaScript/Angular 来流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="fae35-136">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="fae35-137">使用自定义筛选器属性生成文件的防伪令牌，并在 HTTP 头中（而不是在请求正文中）传递该令牌。</span><span class="sxs-lookup"><span data-stu-id="fae35-137">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="fae35-138">由于操作方法直接处理上传的数据，所以其他筛选器会禁用模型绑定。</span><span class="sxs-lookup"><span data-stu-id="fae35-138">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="fae35-139">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="fae35-139">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="fae35-140">读取所有节之后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="fae35-140">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="fae35-141">初始操作加载窗体并将防伪令牌保存在 Cookie 中（通过 `GenerateAntiforgeryTokenCookieForAjax` 属性）：</span><span class="sxs-lookup"><span data-stu-id="fae35-141">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="fae35-142">该属性使用 ASP.NET Core 的内置[防伪](xref:security/anti-request-forgery)支持来设置包含请求令牌的 Cookie：</span><span class="sxs-lookup"><span data-stu-id="fae35-142">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

<span data-ttu-id="fae35-143">Angular 会在名为 `X-XSRF-TOKEN` 的请求标头中自动传递防伪令牌。</span><span class="sxs-lookup"><span data-stu-id="fae35-143">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="fae35-144">ASP.NET Core MVC 应用配置为在 Startup.cs 的配置中引用此标头：</span><span class="sxs-lookup"><span data-stu-id="fae35-144">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

<span data-ttu-id="fae35-145">如下所示的 `DisableFormValueModelBinding` 属性用于禁用针对 `Upload` 操作方法的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="fae35-145">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

<span data-ttu-id="fae35-146">由于已禁用模型绑定，因此 `Upload` 操作方法不接受参数。</span><span class="sxs-lookup"><span data-stu-id="fae35-146">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="fae35-147">它直接使用 `ControllerBase` 的 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="fae35-147">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="fae35-148">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="fae35-148">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="fae35-149">该文件以 GUID 文件名保存，并且键/值数据存储在 `KeyValueAccumulator` 中。</span><span class="sxs-lookup"><span data-stu-id="fae35-149">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="fae35-150">读取所有节之后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="fae35-150">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="fae35-151">完整的 `Upload` 方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="fae35-151">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a><span data-ttu-id="fae35-152">疑难解答</span><span class="sxs-lookup"><span data-stu-id="fae35-152">Troubleshooting</span></span>

<span data-ttu-id="fae35-153">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="fae35-153">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="fae35-154">IIS 发生意外错误“找不到”</span><span class="sxs-lookup"><span data-stu-id="fae35-154">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="fae35-155">以下错误表示文件上传超过服务器配置的 `maxAllowedContentLength`：</span><span class="sxs-lookup"><span data-stu-id="fae35-155">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="fae35-156">默认设置为 `30000000`（大约 28.6MB）。</span><span class="sxs-lookup"><span data-stu-id="fae35-156">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="fae35-157">可通过编辑 web.config 自定义该值：</span><span class="sxs-lookup"><span data-stu-id="fae35-157">The value can be customized by editing *web.config*:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="fae35-158">此设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="fae35-158">This setting only applies to IIS.</span></span> <span data-ttu-id="fae35-159">在 Kestrel 上托管时，默认情况下不会出现此行为。</span><span class="sxs-lookup"><span data-stu-id="fae35-159">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="fae35-160">有关详细信息，请参阅 [Request Limits \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)（请求限制 <requestLimits>）。</span><span class="sxs-lookup"><span data-stu-id="fae35-160">For more information, see [Request Limits \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="fae35-161">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="fae35-161">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="fae35-162">如果控制器正在接受使用 `IFormFile` 上传的文件，但你发现该值始终为 NULL，请确认 HTML 窗体指定的 `enctype` 值是否为 `multipart/form-data`。</span><span class="sxs-lookup"><span data-stu-id="fae35-162">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="fae35-163">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 `IFormFile` 参数都将为 NULL。</span><span class="sxs-lookup"><span data-stu-id="fae35-163">If this attribute isn't set on the `<form>` element, the file upload won't occur and any bound `IFormFile` arguments will be null.</span></span>
