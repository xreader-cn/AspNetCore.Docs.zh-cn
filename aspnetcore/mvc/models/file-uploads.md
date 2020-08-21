---
title: 在 ASP.NET Core 中上传文件
author: rick-anderson
description: 如何在 ASP.NET Core MVC 中使用模型绑定和流式处理上传文件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: 6ff78b26e8e2363cf6c54ebb2a392f390fb2995c
ms.sourcegitcommit: cd412a44f26cb416ceb348fc0a1ccc9a6e9ca73e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720274"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="1390e-103">在 ASP.NET Core 中上传文件</span><span class="sxs-lookup"><span data-stu-id="1390e-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="1390e-104">作者： [Steve Smith](https://ardalis.com/) 和 [Rutger 风暴](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="1390e-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1390e-105">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="1390e-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1390e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1390e-107">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-107">Security considerations</span></span>

<span data-ttu-id="1390e-108">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="1390e-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="1390e-109">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1390e-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="1390e-110">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="1390e-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="1390e-111">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="1390e-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="1390e-112">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="1390e-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="1390e-113">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="1390e-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="1390e-114">将文件上传到专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="1390e-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="1390e-115">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="1390e-116">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="1390e-117">请勿将上传的文件保存在与应用相同的目录树中\*\*\*\*。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="1390e-118">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="1390e-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="1390e-119">请勿使用用户提供的文件名或上载文件的不受信任的文件名。 &dagger; 显示时，HTML 对不受信任的文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="1390e-120">例如，记录文件名或在 UI 中显示 (Razor 会自动对输出) 进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="1390e-121">仅允许应用设计规范的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="1390e-122">验证是否在服务器上执行了客户端检查。 &dagger; 客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="1390e-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="1390e-123">检查已上传文件的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="1390e-124">设置大小上限以防止上传大型文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="1390e-125">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="1390e-126">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="1390e-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="1390e-127">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-128">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="1390e-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="1390e-129">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="1390e-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="1390e-130">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="1390e-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="1390e-131">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="1390e-132">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="1390e-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="1390e-133">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="1390e-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="1390e-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="1390e-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="1390e-135">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="1390e-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="1390e-136">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="1390e-137">存储方案</span><span class="sxs-lookup"><span data-stu-id="1390e-137">Storage scenarios</span></span>

<span data-ttu-id="1390e-138">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="1390e-138">Common storage options for files include:</span></span>

* <span data-ttu-id="1390e-139">数据库</span><span class="sxs-lookup"><span data-stu-id="1390e-139">Database</span></span>

  * <span data-ttu-id="1390e-140">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="1390e-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="1390e-141">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="1390e-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="1390e-142">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="1390e-143">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="1390e-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="1390e-144">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="1390e-144">For large file uploads:</span></span>
    * <span data-ttu-id="1390e-145">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="1390e-146">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="1390e-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="1390e-147">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="1390e-148">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="1390e-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="1390e-149">切勿授予执行权限。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="1390e-150">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="1390e-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="1390e-151">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="1390e-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="1390e-152">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="1390e-153">有关详细信息，请参阅 [快速入门：使用 .net 在对象存储中创建 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="1390e-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="1390e-154">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="1390e-154">File upload scenarios</span></span>

<span data-ttu-id="1390e-155">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="1390e-156">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="1390e-156">**Buffering**</span></span>

<span data-ttu-id="1390e-157">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="1390e-158">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="1390e-159">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="1390e-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="1390e-160">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="1390e-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="1390e-161">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="1390e-162">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="1390e-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="1390e-163">物理存储</span><span class="sxs-lookup"><span data-stu-id="1390e-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="1390e-164">Database</span><span class="sxs-lookup"><span data-stu-id="1390e-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="1390e-165">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="1390e-165">**Streaming**</span></span>

<span data-ttu-id="1390e-166">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="1390e-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="1390e-167">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="1390e-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="1390e-168">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="1390e-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="1390e-169">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="1390e-170">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="1390e-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="1390e-171">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="1390e-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="1390e-172">下面的示例演示 Razor 如何使用页面窗体上传示例应用) 中的单个文件 (*Pages/BufferedSingleFileUploadPhysical* ：</span><span class="sxs-lookup"><span data-stu-id="1390e-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="1390e-173">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="1390e-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="1390e-174">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="1390e-175">无验证。</span><span class="sxs-lookup"><span data-stu-id="1390e-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="1390e-176">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="1390e-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="1390e-177">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="1390e-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="1390e-178">改用 `XMLHttpRequest`</span><span class="sxs-lookup"><span data-stu-id="1390e-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="1390e-179">例如：</span><span class="sxs-lookup"><span data-stu-id="1390e-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="1390e-180">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="1390e-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="1390e-181">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="1390e-182">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="1390e-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="1390e-183">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="1390e-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="1390e-184">除了显示和日志记录用途外，请勿使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="1390e-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="1390e-185">显示或日志记录时，HTML 对文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="1390e-186">攻击者可以提供恶意文件名，包括完整路径或相对路径。</span><span class="sxs-lookup"><span data-stu-id="1390e-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="1390e-187">应用程序应：</span><span class="sxs-lookup"><span data-stu-id="1390e-187">Applications should:</span></span>
>
> * <span data-ttu-id="1390e-188">从用户提供的文件名中删除路径。</span><span class="sxs-lookup"><span data-stu-id="1390e-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="1390e-189">为 UI 或日志记录保存经 HTML 编码、已删除路径的文件名。</span><span class="sxs-lookup"><span data-stu-id="1390e-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="1390e-190">生成新的随机文件名进行存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="1390e-191">以下代码可从文件名中删除路径：</span><span class="sxs-lookup"><span data-stu-id="1390e-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="1390e-192">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="1390e-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="1390e-193">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="1390e-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1390e-194">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1390e-195">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-195">Validation</span></span>](#validation)

<span data-ttu-id="1390e-196">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="1390e-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="1390e-197">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="1390e-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="1390e-198">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="1390e-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="1390e-199">[成员列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="1390e-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="1390e-200">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-200">Binding matches form files by name.</span></span> <span data-ttu-id="1390e-201">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="1390e-202">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="1390e-203">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1390e-203">The following example:</span></span>

* <span data-ttu-id="1390e-204">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="1390e-205">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="1390e-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="1390e-206">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="1390e-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="1390e-207">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="1390e-208">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="1390e-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="1390e-209">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="1390e-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="1390e-210">传递到  的路径必须包含文件名<xref:System.IO.FileStream> \*\*。</span><span class="sxs-lookup"><span data-stu-id="1390e-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="1390e-211">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="1390e-212">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="1390e-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="1390e-213">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="1390e-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="1390e-214">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="1390e-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="1390e-215">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-216">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="1390e-217">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="1390e-218">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="1390e-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="1390e-219">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="1390e-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="1390e-220">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="1390e-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="1390e-221">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="1390e-222">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="1390e-223"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="1390e-224">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="1390e-225">`FileUpload`在 Razor 页面窗体中使用：</span><span class="sxs-lookup"><span data-stu-id="1390e-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="1390e-226">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="1390e-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="1390e-227">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="1390e-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="1390e-228">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="1390e-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="1390e-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1390e-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="1390e-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1390e-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-231">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="1390e-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="1390e-232">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="1390e-233">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="1390e-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="1390e-234">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="1390e-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="1390e-235">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="1390e-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1390e-236">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1390e-237">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="1390e-238">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="1390e-238">Upload large files with streaming</span></span>

<span data-ttu-id="1390e-239">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="1390e-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="1390e-240">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="1390e-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="1390e-241">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="1390e-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="1390e-242">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="1390e-243">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="1390e-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="1390e-244">初始页面响应会加载窗体，并 cookie 通过属性) 将防伪标记保存在 (中 `GenerateAntiforgeryTokenCookieAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="1390e-245">属性使用 ASP.NET Core 的内置 [防伪支持](xref:security/anti-request-forgery) 来设置 cookie 具有请求令牌的：</span><span class="sxs-lookup"><span data-stu-id="1390e-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="1390e-246">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="1390e-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="1390e-247">在示例应用中， `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 页面约定](xref:razor-pages/razor-pages-conventions)作为筛选器应用于和的页面应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="1390e-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="1390e-248">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="1390e-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="1390e-249">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="1390e-250">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="1390e-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="1390e-251">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="1390e-252">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="1390e-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="1390e-253">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="1390e-254">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)：\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="1390e-255">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="1390e-256">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="1390e-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="1390e-257">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-257">Validation</span></span>

<span data-ttu-id="1390e-258">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="1390e-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="1390e-259">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="1390e-260">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-261">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="1390e-262">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="1390e-263">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="1390e-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="1390e-264">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="1390e-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="1390e-265">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="1390e-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="1390e-266">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="1390e-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="1390e-267">内容验证</span><span class="sxs-lookup"><span data-stu-id="1390e-267">Content validation</span></span>

<span data-ttu-id="1390e-268">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="1390e-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="1390e-269">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="1390e-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="1390e-270">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="1390e-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="1390e-271">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="1390e-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="1390e-272">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="1390e-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="1390e-273">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="1390e-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="1390e-274">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="1390e-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="1390e-275">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="1390e-275">File extension validation</span></span>

<span data-ttu-id="1390e-276">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="1390e-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="1390e-277">例如：</span><span class="sxs-lookup"><span data-stu-id="1390e-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="1390e-278">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="1390e-278">File signature validation</span></span>

<span data-ttu-id="1390e-279">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="1390e-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="1390e-280">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="1390e-281">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="1390e-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="1390e-282">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="1390e-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="1390e-283">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="1390e-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="1390e-284">文件名安全</span><span class="sxs-lookup"><span data-stu-id="1390e-284">File name security</span></span>

<span data-ttu-id="1390e-285">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="1390e-286">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="1390e-287">Razor 自动对属性值进行 HTML 编码以便显示。</span><span class="sxs-lookup"><span data-stu-id="1390e-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="1390e-288">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="1390e-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="1390e-289">在之外 Razor ，始终 <xref:System.Net.WebUtility.HtmlEncode*> 根据用户的请求来命名内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="1390e-290">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="1390e-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="1390e-291">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="1390e-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="1390e-292">大小验证</span><span class="sxs-lookup"><span data-stu-id="1390e-292">Size validation</span></span>

<span data-ttu-id="1390e-293">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="1390e-294">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="1390e-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="1390e-295">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="1390e-296">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="1390e-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="1390e-297">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="1390e-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="1390e-298">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="1390e-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="1390e-299">在 Razor 发布窗体数据或直接使用 JavaScript 的非窗体中 `FormData` ，在窗体的元素中指定的名称或 `FormData` 必须与控制器的操作中参数的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="1390e-300">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1390e-300">In the following example:</span></span>

* <span data-ttu-id="1390e-301">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="1390e-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="1390e-302">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="1390e-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="1390e-303">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="1390e-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="1390e-304">对于名为的页 Razor 页面处理程序方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="1390e-305">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="1390e-306">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="1390e-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="1390e-307">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="1390e-307">Multipart body length limit</span></span>

<span data-ttu-id="1390e-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="1390e-309">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="1390e-310">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="1390e-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="1390e-311">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="1390e-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="1390e-312">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="1390e-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="1390e-313">在 Razor 页面应用中，将筛选器应用于中的 [约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="1390e-314">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="1390e-315">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="1390e-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="1390e-316">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="1390e-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1390e-317">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="1390e-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="1390e-318">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="1390e-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="1390e-319">在 Razor 页面应用中，将筛选器应用于中的 [约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="1390e-320">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="1390e-321">`RequestSizeLimitAttribute`还可以使用 [`@attribute`](xref:mvc/views/razor#attribute) Razor 指令应用：</span><span class="sxs-lookup"><span data-stu-id="1390e-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="1390e-322">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="1390e-322">Other Kestrel limits</span></span>

<span data-ttu-id="1390e-323">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="1390e-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="1390e-324">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="1390e-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="1390e-325">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="1390e-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="1390e-326">IIS</span><span class="sxs-lookup"><span data-stu-id="1390e-326">IIS</span></span>

<span data-ttu-id="1390e-327">默认请求限制 (`maxAllowedContentLength`) 为30000000字节，即约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="1390e-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1390e-328">自定义文件中的限制 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="1390e-329">在下面的示例中，将限制设置为 50 MB (52428800 字节) ：</span><span class="sxs-lookup"><span data-stu-id="1390e-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="1390e-330">此 `maxAllowedContentLength` 设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="1390e-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="1390e-331">有关详细信息，请参阅[请求 `<requestLimits>` 限制](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="1390e-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1390e-332">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1390e-332">Troubleshoot</span></span>

<span data-ttu-id="1390e-333">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="1390e-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="1390e-334">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="1390e-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="1390e-335">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="1390e-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="1390e-336">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="1390e-337">连接失败</span><span class="sxs-lookup"><span data-stu-id="1390e-337">Connection failure</span></span>

<span data-ttu-id="1390e-338">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="1390e-339">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="1390e-340">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="1390e-341">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="1390e-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="1390e-342">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="1390e-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="1390e-343">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="1390e-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="1390e-344">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="1390e-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="1390e-345">数据流太长</span><span class="sxs-lookup"><span data-stu-id="1390e-345">Stream was too long</span></span>

<span data-ttu-id="1390e-346">本主题中的示例依赖于 <xref:System.IO.MemoryStream> 来保存已上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="1390e-347">`MemoryStream` 的大小限制为 `int.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="1390e-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="1390e-348">如果应用的文件上传方案要求保存大于 50 MB 的文件内容，请使用另一种方法，该方法不依赖单个 `MemoryStream` 来保存已上传文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="1390e-349">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="1390e-350">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1390e-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1390e-351">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-351">Security considerations</span></span>

<span data-ttu-id="1390e-352">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="1390e-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="1390e-353">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1390e-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="1390e-354">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="1390e-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="1390e-355">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="1390e-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="1390e-356">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="1390e-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="1390e-357">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="1390e-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="1390e-358">将文件上传到专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="1390e-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="1390e-359">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="1390e-360">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="1390e-361">请勿将上传的文件保存在与应用相同的目录树中\*\*\*\*。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="1390e-362">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="1390e-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="1390e-363">请勿使用用户提供的文件名或上载文件的不受信任的文件名。 &dagger; 显示时，HTML 对不受信任的文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="1390e-364">例如，记录文件名或在 UI 中显示 (Razor 会自动对输出) 进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="1390e-365">仅允许应用设计规范的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="1390e-366">验证是否在服务器上执行了客户端检查。 &dagger; 客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="1390e-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="1390e-367">检查已上传文件的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="1390e-368">设置大小上限以防止上传大型文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="1390e-369">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="1390e-370">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="1390e-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="1390e-371">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-372">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="1390e-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="1390e-373">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="1390e-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="1390e-374">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="1390e-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="1390e-375">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="1390e-376">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="1390e-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="1390e-377">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="1390e-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="1390e-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="1390e-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="1390e-379">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="1390e-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="1390e-380">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="1390e-381">存储方案</span><span class="sxs-lookup"><span data-stu-id="1390e-381">Storage scenarios</span></span>

<span data-ttu-id="1390e-382">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="1390e-382">Common storage options for files include:</span></span>

* <span data-ttu-id="1390e-383">数据库</span><span class="sxs-lookup"><span data-stu-id="1390e-383">Database</span></span>

  * <span data-ttu-id="1390e-384">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="1390e-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="1390e-385">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="1390e-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="1390e-386">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="1390e-387">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="1390e-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="1390e-388">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="1390e-388">For large file uploads:</span></span>
    * <span data-ttu-id="1390e-389">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="1390e-390">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="1390e-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="1390e-391">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="1390e-392">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="1390e-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="1390e-393">切勿授予执行权限。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="1390e-394">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="1390e-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="1390e-395">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="1390e-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="1390e-396">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="1390e-397">有关详细信息，请参阅 [快速入门：使用 .net 在对象存储中创建 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="1390e-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="1390e-398">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="1390e-398">File upload scenarios</span></span>

<span data-ttu-id="1390e-399">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="1390e-400">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="1390e-400">**Buffering**</span></span>

<span data-ttu-id="1390e-401">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="1390e-402">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="1390e-403">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="1390e-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="1390e-404">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="1390e-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="1390e-405">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="1390e-406">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="1390e-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="1390e-407">物理存储</span><span class="sxs-lookup"><span data-stu-id="1390e-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="1390e-408">Database</span><span class="sxs-lookup"><span data-stu-id="1390e-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="1390e-409">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="1390e-409">**Streaming**</span></span>

<span data-ttu-id="1390e-410">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="1390e-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="1390e-411">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="1390e-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="1390e-412">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="1390e-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="1390e-413">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="1390e-414">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="1390e-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="1390e-415">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="1390e-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="1390e-416">下面的示例演示 Razor 如何使用页面窗体上传示例应用) 中的单个文件 (*Pages/BufferedSingleFileUploadPhysical* ：</span><span class="sxs-lookup"><span data-stu-id="1390e-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="1390e-417">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="1390e-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="1390e-418">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="1390e-419">无验证。</span><span class="sxs-lookup"><span data-stu-id="1390e-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="1390e-420">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="1390e-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="1390e-421">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="1390e-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="1390e-422">改用 `XMLHttpRequest`</span><span class="sxs-lookup"><span data-stu-id="1390e-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="1390e-423">例如：</span><span class="sxs-lookup"><span data-stu-id="1390e-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="1390e-424">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="1390e-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="1390e-425">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="1390e-426">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="1390e-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="1390e-427">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="1390e-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="1390e-428">除了显示和日志记录用途外，请勿使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="1390e-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="1390e-429">显示或日志记录时，HTML 对文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="1390e-430">攻击者可以提供恶意文件名，包括完整路径或相对路径。</span><span class="sxs-lookup"><span data-stu-id="1390e-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="1390e-431">应用程序应：</span><span class="sxs-lookup"><span data-stu-id="1390e-431">Applications should:</span></span>
>
> * <span data-ttu-id="1390e-432">从用户提供的文件名中删除路径。</span><span class="sxs-lookup"><span data-stu-id="1390e-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="1390e-433">为 UI 或日志记录保存经 HTML 编码、已删除路径的文件名。</span><span class="sxs-lookup"><span data-stu-id="1390e-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="1390e-434">生成新的随机文件名进行存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="1390e-435">以下代码可从文件名中删除路径：</span><span class="sxs-lookup"><span data-stu-id="1390e-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="1390e-436">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="1390e-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="1390e-437">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="1390e-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1390e-438">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1390e-439">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-439">Validation</span></span>](#validation)

<span data-ttu-id="1390e-440">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="1390e-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="1390e-441">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="1390e-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="1390e-442">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="1390e-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="1390e-443">[成员列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="1390e-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="1390e-444">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-444">Binding matches form files by name.</span></span> <span data-ttu-id="1390e-445">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="1390e-446">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="1390e-447">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1390e-447">The following example:</span></span>

* <span data-ttu-id="1390e-448">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="1390e-449">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="1390e-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="1390e-450">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="1390e-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="1390e-451">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="1390e-452">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="1390e-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="1390e-453">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="1390e-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="1390e-454">传递到  的路径必须包含文件名<xref:System.IO.FileStream> \*\*。</span><span class="sxs-lookup"><span data-stu-id="1390e-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="1390e-455">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="1390e-456">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="1390e-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="1390e-457">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="1390e-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="1390e-458">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="1390e-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="1390e-459">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-460">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="1390e-461">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="1390e-462">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="1390e-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="1390e-463">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="1390e-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="1390e-464">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="1390e-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="1390e-465">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="1390e-466">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="1390e-467"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="1390e-468">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="1390e-469">`FileUpload`在 Razor 页面窗体中使用：</span><span class="sxs-lookup"><span data-stu-id="1390e-469">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="1390e-470">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="1390e-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="1390e-471">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="1390e-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="1390e-472">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="1390e-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="1390e-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1390e-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="1390e-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1390e-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-475">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="1390e-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="1390e-476">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="1390e-477">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="1390e-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="1390e-478">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="1390e-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="1390e-479">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="1390e-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1390e-480">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1390e-481">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="1390e-482">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="1390e-482">Upload large files with streaming</span></span>

<span data-ttu-id="1390e-483">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="1390e-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="1390e-484">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="1390e-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="1390e-485">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="1390e-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="1390e-486">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="1390e-487">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="1390e-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="1390e-488">初始页面响应会加载窗体，并 cookie 通过属性) 将防伪标记保存在 (中 `GenerateAntiforgeryTokenCookieAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="1390e-489">属性使用 ASP.NET Core 的内置 [防伪支持](xref:security/anti-request-forgery) 来设置 cookie 具有请求令牌的：</span><span class="sxs-lookup"><span data-stu-id="1390e-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="1390e-490">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="1390e-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="1390e-491">在示例应用中， `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 页面约定](xref:razor-pages/razor-pages-conventions)作为筛选器应用于和的页面应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="1390e-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="1390e-492">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="1390e-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="1390e-493">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="1390e-494">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="1390e-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="1390e-495">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="1390e-496">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="1390e-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="1390e-497">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="1390e-498">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)：\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="1390e-499">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="1390e-500">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="1390e-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="1390e-501">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-501">Validation</span></span>

<span data-ttu-id="1390e-502">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="1390e-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="1390e-503">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="1390e-504">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-505">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="1390e-506">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="1390e-507">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="1390e-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="1390e-508">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="1390e-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="1390e-509">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="1390e-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="1390e-510">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="1390e-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="1390e-511">内容验证</span><span class="sxs-lookup"><span data-stu-id="1390e-511">Content validation</span></span>

<span data-ttu-id="1390e-512">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="1390e-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="1390e-513">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="1390e-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="1390e-514">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="1390e-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="1390e-515">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="1390e-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="1390e-516">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="1390e-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="1390e-517">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="1390e-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="1390e-518">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="1390e-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="1390e-519">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="1390e-519">File extension validation</span></span>

<span data-ttu-id="1390e-520">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="1390e-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="1390e-521">例如：</span><span class="sxs-lookup"><span data-stu-id="1390e-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="1390e-522">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="1390e-522">File signature validation</span></span>

<span data-ttu-id="1390e-523">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="1390e-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="1390e-524">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="1390e-525">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="1390e-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="1390e-526">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="1390e-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="1390e-527">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="1390e-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="1390e-528">文件名安全</span><span class="sxs-lookup"><span data-stu-id="1390e-528">File name security</span></span>

<span data-ttu-id="1390e-529">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="1390e-530">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="1390e-531">Razor 自动对属性值进行 HTML 编码以便显示。</span><span class="sxs-lookup"><span data-stu-id="1390e-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="1390e-532">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="1390e-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="1390e-533">在之外 Razor ，始终 <xref:System.Net.WebUtility.HtmlEncode*> 根据用户的请求来命名内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="1390e-534">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="1390e-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="1390e-535">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="1390e-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="1390e-536">大小验证</span><span class="sxs-lookup"><span data-stu-id="1390e-536">Size validation</span></span>

<span data-ttu-id="1390e-537">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="1390e-538">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="1390e-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="1390e-539">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="1390e-540">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="1390e-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="1390e-541">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="1390e-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="1390e-542">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="1390e-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="1390e-543">在 Razor 发布窗体数据或直接使用 JavaScript 的非窗体中 `FormData` ，在窗体的元素中指定的名称或 `FormData` 必须与控制器的操作中参数的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="1390e-544">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1390e-544">In the following example:</span></span>

* <span data-ttu-id="1390e-545">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="1390e-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="1390e-546">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="1390e-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="1390e-547">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="1390e-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="1390e-548">对于名为的页 Razor 页面处理程序方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="1390e-549">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="1390e-550">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="1390e-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="1390e-551">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="1390e-551">Multipart body length limit</span></span>

<span data-ttu-id="1390e-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="1390e-553">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="1390e-554">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="1390e-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="1390e-555">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="1390e-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="1390e-556">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="1390e-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="1390e-557">在 Razor 页面应用中，将筛选器应用于中的 [约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="1390e-558">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="1390e-559">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="1390e-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="1390e-560">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="1390e-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1390e-561">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="1390e-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="1390e-562">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="1390e-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="1390e-563">在 Razor 页面应用中，将筛选器应用于中的 [约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="1390e-564">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="1390e-565">`RequestSizeLimitAttribute`还可以使用 [`@attribute`](xref:mvc/views/razor#attribute) Razor 指令应用：</span><span class="sxs-lookup"><span data-stu-id="1390e-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="1390e-566">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="1390e-566">Other Kestrel limits</span></span>

<span data-ttu-id="1390e-567">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="1390e-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="1390e-568">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="1390e-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="1390e-569">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="1390e-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="1390e-570">IIS</span><span class="sxs-lookup"><span data-stu-id="1390e-570">IIS</span></span>

<span data-ttu-id="1390e-571">默认请求限制 (`maxAllowedContentLength`) 为30000000字节，即约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="1390e-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1390e-572">自定义文件中的限制 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="1390e-573">在下面的示例中，将限制设置为 50 MB (52428800 字节) ：</span><span class="sxs-lookup"><span data-stu-id="1390e-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="1390e-574">此 `maxAllowedContentLength` 设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="1390e-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="1390e-575">有关详细信息，请参阅[请求 `<requestLimits>` 限制](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="1390e-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="1390e-576">通过在中设置来增加 HTTP 请求的最大请求正文大小 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1390e-577">在下面的示例中，将限制设置为 50 MB (52428800 字节) ：</span><span class="sxs-lookup"><span data-stu-id="1390e-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="1390e-578">有关详细信息，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="1390e-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1390e-579">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1390e-579">Troubleshoot</span></span>

<span data-ttu-id="1390e-580">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="1390e-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="1390e-581">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="1390e-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="1390e-582">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="1390e-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="1390e-583">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="1390e-584">连接失败</span><span class="sxs-lookup"><span data-stu-id="1390e-584">Connection failure</span></span>

<span data-ttu-id="1390e-585">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="1390e-586">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="1390e-587">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="1390e-588">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="1390e-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="1390e-589">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="1390e-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="1390e-590">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="1390e-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="1390e-591">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="1390e-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="1390e-592">数据流太长</span><span class="sxs-lookup"><span data-stu-id="1390e-592">Stream was too long</span></span>

<span data-ttu-id="1390e-593">本主题中的示例依赖于 <xref:System.IO.MemoryStream> 来保存已上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="1390e-594">`MemoryStream` 的大小限制为 `int.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="1390e-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="1390e-595">如果应用的文件上传方案要求保存大于 50 MB 的文件内容，请使用另一种方法，该方法不依赖单个 `MemoryStream` 来保存已上传文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1390e-596">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="1390e-597">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1390e-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1390e-598">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-598">Security considerations</span></span>

<span data-ttu-id="1390e-599">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="1390e-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="1390e-600">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1390e-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="1390e-601">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="1390e-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="1390e-602">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="1390e-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="1390e-603">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="1390e-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="1390e-604">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="1390e-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="1390e-605">将文件上传到专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="1390e-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="1390e-606">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="1390e-607">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="1390e-608">请勿将上传的文件保存在与应用相同的目录树中\*\*\*\*。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="1390e-609">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="1390e-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="1390e-610">请勿使用用户提供的文件名或上载文件的不受信任的文件名。 &dagger; 显示时，HTML 对不受信任的文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="1390e-611">例如，记录文件名或在 UI 中显示 (Razor 会自动对输出) 进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="1390e-612">仅允许应用设计规范的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="1390e-613">验证是否在服务器上执行了客户端检查。 &dagger; 客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="1390e-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="1390e-614">检查已上传文件的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="1390e-615">设置大小上限以防止上传大型文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="1390e-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="1390e-616">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="1390e-617">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="1390e-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="1390e-618">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-619">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="1390e-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="1390e-620">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="1390e-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="1390e-621">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="1390e-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="1390e-622">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="1390e-623">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="1390e-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="1390e-624">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="1390e-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="1390e-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="1390e-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="1390e-626">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="1390e-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="1390e-627">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="1390e-628">存储方案</span><span class="sxs-lookup"><span data-stu-id="1390e-628">Storage scenarios</span></span>

<span data-ttu-id="1390e-629">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="1390e-629">Common storage options for files include:</span></span>

* <span data-ttu-id="1390e-630">数据库</span><span class="sxs-lookup"><span data-stu-id="1390e-630">Database</span></span>

  * <span data-ttu-id="1390e-631">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="1390e-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="1390e-632">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="1390e-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="1390e-633">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="1390e-634">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="1390e-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="1390e-635">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="1390e-635">For large file uploads:</span></span>
    * <span data-ttu-id="1390e-636">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="1390e-637">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="1390e-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="1390e-638">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="1390e-639">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="1390e-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="1390e-640">切勿授予执行权限。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="1390e-641">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="1390e-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="1390e-642">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="1390e-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="1390e-643">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="1390e-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="1390e-644">有关详细信息，请参阅 [快速入门：使用 .net 在对象存储中创建 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="1390e-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="1390e-645">此主题说明了 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>，但在处理 <xref:System.IO.Stream> 时，可以使用 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 将 <xref:System.IO.FileStream> 保存到 blob 存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="1390e-646">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="1390e-646">File upload scenarios</span></span>

<span data-ttu-id="1390e-647">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="1390e-648">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="1390e-648">**Buffering**</span></span>

<span data-ttu-id="1390e-649">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="1390e-650">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="1390e-651">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="1390e-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="1390e-652">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="1390e-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="1390e-653">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="1390e-654">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="1390e-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="1390e-655">物理存储</span><span class="sxs-lookup"><span data-stu-id="1390e-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="1390e-656">Database</span><span class="sxs-lookup"><span data-stu-id="1390e-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="1390e-657">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="1390e-657">**Streaming**</span></span>

<span data-ttu-id="1390e-658">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="1390e-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="1390e-659">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="1390e-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="1390e-660">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="1390e-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="1390e-661">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="1390e-662">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="1390e-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="1390e-663">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="1390e-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="1390e-664">下面的示例演示 Razor 如何使用页面窗体上传示例应用) 中的单个文件 (*Pages/BufferedSingleFileUploadPhysical* ：</span><span class="sxs-lookup"><span data-stu-id="1390e-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="1390e-665">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="1390e-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="1390e-666">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="1390e-667">无验证。</span><span class="sxs-lookup"><span data-stu-id="1390e-667">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="1390e-668">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="1390e-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="1390e-669">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="1390e-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="1390e-670">改用 `XMLHttpRequest`</span><span class="sxs-lookup"><span data-stu-id="1390e-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="1390e-671">例如：</span><span class="sxs-lookup"><span data-stu-id="1390e-671">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="1390e-672">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="1390e-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="1390e-673">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="1390e-674">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="1390e-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="1390e-675">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="1390e-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="1390e-676">除了显示和日志记录用途外，请勿使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="1390e-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="1390e-677">显示或日志记录时，HTML 对文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="1390e-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="1390e-678">攻击者可以提供恶意文件名，包括完整路径或相对路径。</span><span class="sxs-lookup"><span data-stu-id="1390e-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="1390e-679">应用程序应：</span><span class="sxs-lookup"><span data-stu-id="1390e-679">Applications should:</span></span>
>
> * <span data-ttu-id="1390e-680">从用户提供的文件名中删除路径。</span><span class="sxs-lookup"><span data-stu-id="1390e-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="1390e-681">为 UI 或日志记录保存经 HTML 编码、已删除路径的文件名。</span><span class="sxs-lookup"><span data-stu-id="1390e-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="1390e-682">生成新的随机文件名进行存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="1390e-683">以下代码可从文件名中删除路径：</span><span class="sxs-lookup"><span data-stu-id="1390e-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="1390e-684">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="1390e-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="1390e-685">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="1390e-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1390e-686">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1390e-687">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-687">Validation</span></span>](#validation)

<span data-ttu-id="1390e-688">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="1390e-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="1390e-689">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="1390e-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="1390e-690">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="1390e-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="1390e-691">[成员列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="1390e-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="1390e-692">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-692">Binding matches form files by name.</span></span> <span data-ttu-id="1390e-693">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="1390e-694">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="1390e-695">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1390e-695">The following example:</span></span>

* <span data-ttu-id="1390e-696">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="1390e-697">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="1390e-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="1390e-698">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="1390e-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="1390e-699">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-699">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="1390e-700">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="1390e-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="1390e-701">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="1390e-701">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="1390e-702">传递到  的路径必须包含文件名<xref:System.IO.FileStream> \*\*。</span><span class="sxs-lookup"><span data-stu-id="1390e-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="1390e-703">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="1390e-704">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="1390e-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="1390e-705">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="1390e-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="1390e-706">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="1390e-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="1390e-707">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-708">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="1390e-709">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="1390e-710">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="1390e-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="1390e-711">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="1390e-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="1390e-712">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="1390e-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="1390e-713">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="1390e-714">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="1390e-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="1390e-715"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="1390e-716">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="1390e-717">`FileUpload`在 Razor 页面窗体中使用：</span><span class="sxs-lookup"><span data-stu-id="1390e-717">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="1390e-718">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="1390e-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="1390e-719">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="1390e-719">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="1390e-720">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="1390e-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="1390e-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1390e-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="1390e-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1390e-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-723">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="1390e-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="1390e-724">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="1390e-725">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="1390e-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="1390e-726">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="1390e-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="1390e-727">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="1390e-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="1390e-728">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="1390e-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="1390e-729">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="1390e-730">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="1390e-730">Upload large files with streaming</span></span>

<span data-ttu-id="1390e-731">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="1390e-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="1390e-732">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="1390e-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="1390e-733">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="1390e-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="1390e-734">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="1390e-735">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="1390e-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="1390e-736">初始页面响应会加载窗体，并 cookie 通过属性) 将防伪标记保存在 (中 `GenerateAntiforgeryTokenCookieAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="1390e-737">属性使用 ASP.NET Core 的内置 [防伪支持](xref:security/anti-request-forgery) 来设置 cookie 具有请求令牌的：</span><span class="sxs-lookup"><span data-stu-id="1390e-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="1390e-738">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="1390e-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="1390e-739">在示例应用中， `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 页面约定](xref:razor-pages/razor-pages-conventions)作为筛选器应用于和的页面应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="1390e-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="1390e-740">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="1390e-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="1390e-741">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="1390e-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="1390e-742">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="1390e-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="1390e-743">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="1390e-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="1390e-744">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="1390e-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="1390e-745">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="1390e-746">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)：\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="1390e-747">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="1390e-748">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="1390e-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="1390e-749">验证</span><span class="sxs-lookup"><span data-stu-id="1390e-749">Validation</span></span>

<span data-ttu-id="1390e-750">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="1390e-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="1390e-751">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="1390e-752">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="1390e-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="1390e-753">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="1390e-754">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="1390e-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="1390e-755">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="1390e-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="1390e-756">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="1390e-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="1390e-757">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="1390e-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="1390e-758">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="1390e-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="1390e-759">内容验证</span><span class="sxs-lookup"><span data-stu-id="1390e-759">Content validation</span></span>

<span data-ttu-id="1390e-760">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="1390e-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="1390e-761">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="1390e-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="1390e-762">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="1390e-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="1390e-763">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="1390e-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="1390e-764">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="1390e-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="1390e-765">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="1390e-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="1390e-766">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="1390e-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="1390e-767">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="1390e-767">File extension validation</span></span>

<span data-ttu-id="1390e-768">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="1390e-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="1390e-769">例如：</span><span class="sxs-lookup"><span data-stu-id="1390e-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="1390e-770">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="1390e-770">File signature validation</span></span>

<span data-ttu-id="1390e-771">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="1390e-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="1390e-772">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="1390e-773">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="1390e-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="1390e-774">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="1390e-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="1390e-775">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="1390e-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="1390e-776">文件名安全</span><span class="sxs-lookup"><span data-stu-id="1390e-776">File name security</span></span>

<span data-ttu-id="1390e-777">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="1390e-778">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="1390e-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="1390e-779">Razor 自动对属性值进行 HTML 编码以便显示。</span><span class="sxs-lookup"><span data-stu-id="1390e-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="1390e-780">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="1390e-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="1390e-781">在之外 Razor ，始终 <xref:System.Net.WebUtility.HtmlEncode*> 根据用户的请求来命名内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="1390e-782">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="1390e-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="1390e-783">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="1390e-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="1390e-784">大小验证</span><span class="sxs-lookup"><span data-stu-id="1390e-784">Size validation</span></span>

<span data-ttu-id="1390e-785">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="1390e-786">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="1390e-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="1390e-787">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="1390e-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="1390e-788">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="1390e-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="1390e-789">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="1390e-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="1390e-790">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="1390e-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="1390e-791">在 Razor 发布窗体数据或直接使用 JavaScript 的非窗体中 `FormData` ，在窗体的元素中指定的名称或 `FormData` 必须与控制器的操作中参数的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="1390e-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="1390e-792">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1390e-792">In the following example:</span></span>

* <span data-ttu-id="1390e-793">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="1390e-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="1390e-794">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="1390e-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="1390e-795">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="1390e-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="1390e-796">对于名为的页 Razor 页面处理程序方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="1390e-797">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="1390e-798">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="1390e-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="1390e-799">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="1390e-799">Multipart body length limit</span></span>

<span data-ttu-id="1390e-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="1390e-801">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="1390e-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="1390e-802">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="1390e-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="1390e-803">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="1390e-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="1390e-804">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="1390e-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="1390e-805">在 Razor 页面应用中，将筛选器应用于中的 [约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="1390e-806">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="1390e-807">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="1390e-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="1390e-808">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="1390e-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1390e-809">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="1390e-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="1390e-810">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="1390e-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="1390e-811">在 Razor 页面应用中，将筛选器应用于中的 [约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="1390e-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="1390e-812">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1390e-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="1390e-813">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="1390e-813">Other Kestrel limits</span></span>

<span data-ttu-id="1390e-814">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="1390e-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="1390e-815">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="1390e-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="1390e-816">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="1390e-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="1390e-817">IIS</span><span class="sxs-lookup"><span data-stu-id="1390e-817">IIS</span></span>

<span data-ttu-id="1390e-818">默认请求限制 (`maxAllowedContentLength`) 为30000000字节，即约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="1390e-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="1390e-819">自定义文件中的限制 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="1390e-820">在下面的示例中，将限制设置为 50 MB (52428800 字节) ：</span><span class="sxs-lookup"><span data-stu-id="1390e-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="1390e-821">此 `maxAllowedContentLength` 设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="1390e-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="1390e-822">有关详细信息，请参阅[请求 `<requestLimits>` 限制](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="1390e-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="1390e-823">通过在中设置来增加 HTTP 请求的最大请求正文大小 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="1390e-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1390e-824">在下面的示例中，将限制设置为 50 MB (52428800 字节) ：</span><span class="sxs-lookup"><span data-stu-id="1390e-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="1390e-825">有关详细信息，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="1390e-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1390e-826">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1390e-826">Troubleshoot</span></span>

<span data-ttu-id="1390e-827">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="1390e-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="1390e-828">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="1390e-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="1390e-829">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="1390e-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="1390e-830">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="1390e-831">连接失败</span><span class="sxs-lookup"><span data-stu-id="1390e-831">Connection failure</span></span>

<span data-ttu-id="1390e-832">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="1390e-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="1390e-833">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="1390e-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="1390e-834">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="1390e-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="1390e-835">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="1390e-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="1390e-836">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="1390e-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="1390e-837">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="1390e-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="1390e-838">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="1390e-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="1390e-839">数据流太长</span><span class="sxs-lookup"><span data-stu-id="1390e-839">Stream was too long</span></span>

<span data-ttu-id="1390e-840">本主题中的示例依赖于 <xref:System.IO.MemoryStream> 来保存已上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="1390e-841">`MemoryStream` 的大小限制为 `int.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="1390e-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="1390e-842">如果应用的文件上传方案要求保存大于 50 MB 的文件内容，请使用另一种方法，该方法不依赖单个 `MemoryStream` 来保存已上传文件的内容。</span><span class="sxs-lookup"><span data-stu-id="1390e-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="1390e-843">其他资源</span><span class="sxs-lookup"><span data-stu-id="1390e-843">Additional resources</span></span>

* [<span data-ttu-id="1390e-844">HTTP 连接请求排出</span><span class="sxs-lookup"><span data-stu-id="1390e-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="1390e-845">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="1390e-845">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="1390e-846">Azure 安全：安全框架：输入验证 |措施</span><span class="sxs-lookup"><span data-stu-id="1390e-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="1390e-847">Azure 云设计模式：附属密钥模式</span><span class="sxs-lookup"><span data-stu-id="1390e-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
