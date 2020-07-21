---
title: 在 ASP.NET Core 中上传文件
author: rick-anderson
description: 如何在 ASP.NET Core MVC 中使用模型绑定和流式处理上传文件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: 720da8a8fe22f0e1911fd554c094661b4465a335
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86568829"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="5099f-103">在 ASP.NET Core 中上传文件</span><span class="sxs-lookup"><span data-stu-id="5099f-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="5099f-104">作者： [Steve Smith](https://ardalis.com/)和[Rutger 风暴](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="5099f-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5099f-105">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="5099f-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5099f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="5099f-107">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="5099f-107">Security considerations</span></span>

<span data-ttu-id="5099f-108">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="5099f-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="5099f-109">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5099f-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="5099f-110">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="5099f-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="5099f-111">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="5099f-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="5099f-112">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="5099f-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="5099f-113">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="5099f-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="5099f-114">将文件上传到专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="5099f-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="5099f-115">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="5099f-116">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="5099f-117">请勿将上传的文件保存在与应用相同的目录树中\*\*\*\*。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="5099f-118">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="5099f-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="5099f-119">请勿使用用户提供的文件名或上载文件的不受信任的文件名。 &dagger;显示时，HTML 对不受信任的文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="5099f-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="5099f-120">例如，记录文件名或在 UI 中显示（自动对 Razor 输出进行 HTML 编码）。</span><span class="sxs-lookup"><span data-stu-id="5099f-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="5099f-121">仅允许应用设计规范的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="5099f-122">验证是否在服务器上执行了客户端检查。 &dagger;客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="5099f-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="5099f-123">检查已上传文件的大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="5099f-124">设置大小上限以防止上传大型文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="5099f-125">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="5099f-126">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="5099f-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="5099f-127">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="5099f-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-128">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="5099f-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="5099f-129">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="5099f-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="5099f-130">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="5099f-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="5099f-131">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="5099f-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="5099f-132">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="5099f-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="5099f-133">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="5099f-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="5099f-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="5099f-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="5099f-135">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="5099f-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="5099f-136">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="5099f-137">存储方案</span><span class="sxs-lookup"><span data-stu-id="5099f-137">Storage scenarios</span></span>

<span data-ttu-id="5099f-138">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="5099f-138">Common storage options for files include:</span></span>

* <span data-ttu-id="5099f-139">数据库</span><span class="sxs-lookup"><span data-stu-id="5099f-139">Database</span></span>

  * <span data-ttu-id="5099f-140">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="5099f-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="5099f-141">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="5099f-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="5099f-142">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="5099f-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="5099f-143">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="5099f-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="5099f-144">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="5099f-144">For large file uploads:</span></span>
    * <span data-ttu-id="5099f-145">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="5099f-146">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="5099f-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="5099f-147">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="5099f-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="5099f-148">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="5099f-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="5099f-149">切勿授予执行权限。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="5099f-150">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="5099f-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="5099f-151">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="5099f-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="5099f-152">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="5099f-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="5099f-153">有关详细信息，请参阅[快速入门：使用 .net 在对象存储中创建 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="5099f-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="5099f-154">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="5099f-154">File upload scenarios</span></span>

<span data-ttu-id="5099f-155">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="5099f-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="5099f-156">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="5099f-156">**Buffering**</span></span>

<span data-ttu-id="5099f-157">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="5099f-158">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="5099f-159">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="5099f-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="5099f-160">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="5099f-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="5099f-161">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="5099f-162">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="5099f-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="5099f-163">物理存储</span><span class="sxs-lookup"><span data-stu-id="5099f-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="5099f-164">Database</span><span class="sxs-lookup"><span data-stu-id="5099f-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="5099f-165">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="5099f-165">**Streaming**</span></span>

<span data-ttu-id="5099f-166">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="5099f-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="5099f-167">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="5099f-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="5099f-168">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="5099f-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="5099f-169">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="5099f-170">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="5099f-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="5099f-171">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="5099f-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="5099f-172">下面的示例演示 Razor 如何使用页面窗体上传一个文件（示例应用中的*Pages/BufferedSingleFileUploadPhysical* ）：</span><span class="sxs-lookup"><span data-stu-id="5099f-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="5099f-173">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="5099f-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="5099f-174">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="5099f-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="5099f-175">无验证。</span><span class="sxs-lookup"><span data-stu-id="5099f-175">There's no validation.</span></span>

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

<span data-ttu-id="5099f-176">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="5099f-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="5099f-177">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="5099f-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="5099f-178">改用 `XMLHttpRequest`</span><span class="sxs-lookup"><span data-stu-id="5099f-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="5099f-179">例如：</span><span class="sxs-lookup"><span data-stu-id="5099f-179">For example:</span></span>

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

<span data-ttu-id="5099f-180">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="5099f-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="5099f-181">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="5099f-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="5099f-182">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="5099f-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="5099f-183">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="5099f-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="5099f-184">除了显示和日志记录用途外，请勿使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5099f-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="5099f-185">显示或日志记录时，HTML 对文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="5099f-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="5099f-186">攻击者可以提供恶意文件名，包括完整路径或相对路径。</span><span class="sxs-lookup"><span data-stu-id="5099f-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="5099f-187">应用程序应：</span><span class="sxs-lookup"><span data-stu-id="5099f-187">Applications should:</span></span>
>
> * <span data-ttu-id="5099f-188">从用户提供的文件名中删除路径。</span><span class="sxs-lookup"><span data-stu-id="5099f-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="5099f-189">为 UI 或日志记录保存经 HTML 编码、已删除路径的文件名。</span><span class="sxs-lookup"><span data-stu-id="5099f-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="5099f-190">生成新的随机文件名进行存储。</span><span class="sxs-lookup"><span data-stu-id="5099f-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="5099f-191">以下代码可从文件名中删除路径：</span><span class="sxs-lookup"><span data-stu-id="5099f-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="5099f-192">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="5099f-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="5099f-193">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="5099f-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5099f-194">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="5099f-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5099f-195">验证</span><span class="sxs-lookup"><span data-stu-id="5099f-195">Validation</span></span>](#validation)

<span data-ttu-id="5099f-196">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="5099f-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="5099f-197">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="5099f-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="5099f-198">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="5099f-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="5099f-199">[成员列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="5099f-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="5099f-200">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-200">Binding matches form files by name.</span></span> <span data-ttu-id="5099f-201">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="5099f-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="5099f-202">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="5099f-203">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5099f-203">The following example:</span></span>

* <span data-ttu-id="5099f-204">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="5099f-205">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="5099f-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="5099f-206">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="5099f-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="5099f-207">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="5099f-208">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="5099f-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="5099f-209">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="5099f-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="5099f-210">传递到  的路径必须包含文件名<xref:System.IO.FileStream> \*\*。</span><span class="sxs-lookup"><span data-stu-id="5099f-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="5099f-211">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="5099f-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="5099f-212">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="5099f-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="5099f-213">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="5099f-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="5099f-214">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="5099f-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="5099f-215">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-216">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="5099f-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="5099f-217">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="5099f-218">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="5099f-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="5099f-219">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="5099f-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="5099f-220">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="5099f-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="5099f-221">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="5099f-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="5099f-222">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="5099f-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="5099f-223"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="5099f-224">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="5099f-225">`FileUpload`在 Razor 页面窗体中使用：</span><span class="sxs-lookup"><span data-stu-id="5099f-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="5099f-226">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="5099f-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="5099f-227">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="5099f-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="5099f-228">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="5099f-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="5099f-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5099f-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="5099f-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5099f-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-231">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="5099f-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="5099f-232">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="5099f-233">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="5099f-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="5099f-234">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="5099f-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="5099f-235">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="5099f-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5099f-236">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="5099f-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5099f-237">验证</span><span class="sxs-lookup"><span data-stu-id="5099f-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="5099f-238">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="5099f-238">Upload large files with streaming</span></span>

<span data-ttu-id="5099f-239">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5099f-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="5099f-240">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="5099f-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="5099f-241">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="5099f-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="5099f-242">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="5099f-243">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="5099f-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="5099f-244">初始页响应加载窗体并将防伪令牌保存在 Cookie 中（通过 `GenerateAntiforgeryTokenCookieAttribute` 属性）。</span><span class="sxs-lookup"><span data-stu-id="5099f-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="5099f-245">该属性使用 ASP.NET Core 的内置[防伪支持](xref:security/anti-request-forgery)来设置带有请求令牌的 cookie：</span><span class="sxs-lookup"><span data-stu-id="5099f-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="5099f-246">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="5099f-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="5099f-247">在示例应用中， `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 页面约定](xref:razor-pages/razor-pages-conventions)作为筛选器应用于和的页面应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="5099f-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="5099f-248">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="5099f-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="5099f-249">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="5099f-250">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="5099f-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="5099f-251">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="5099f-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="5099f-252">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="5099f-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="5099f-253">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="5099f-254">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)：\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="5099f-255">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="5099f-256">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="5099f-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="5099f-257">验证</span><span class="sxs-lookup"><span data-stu-id="5099f-257">Validation</span></span>

<span data-ttu-id="5099f-258">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="5099f-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="5099f-259">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="5099f-260">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="5099f-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-261">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="5099f-262">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="5099f-263">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="5099f-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="5099f-264">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="5099f-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="5099f-265">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="5099f-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="5099f-266">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="5099f-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="5099f-267">内容验证</span><span class="sxs-lookup"><span data-stu-id="5099f-267">Content validation</span></span>

<span data-ttu-id="5099f-268">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="5099f-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="5099f-269">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="5099f-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="5099f-270">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="5099f-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="5099f-271">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="5099f-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="5099f-272">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="5099f-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="5099f-273">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="5099f-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="5099f-274">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="5099f-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="5099f-275">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="5099f-275">File extension validation</span></span>

<span data-ttu-id="5099f-276">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="5099f-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="5099f-277">例如：</span><span class="sxs-lookup"><span data-stu-id="5099f-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="5099f-278">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="5099f-278">File signature validation</span></span>

<span data-ttu-id="5099f-279">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="5099f-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="5099f-280">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="5099f-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="5099f-281">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="5099f-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="5099f-282">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="5099f-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="5099f-283">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="5099f-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="5099f-284">文件名安全</span><span class="sxs-lookup"><span data-stu-id="5099f-284">File name security</span></span>

<span data-ttu-id="5099f-285">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="5099f-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="5099f-286">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="5099f-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="5099f-287">自动对属性值进行 HTML 编码以便显示。</span><span class="sxs-lookup"><span data-stu-id="5099f-287"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="5099f-288">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="5099f-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="5099f-289">在之外 Razor ，始终 <xref:System.Net.WebUtility.HtmlEncode*> 根据用户的请求来命名内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="5099f-290">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="5099f-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="5099f-291">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="5099f-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="5099f-292">大小验证</span><span class="sxs-lookup"><span data-stu-id="5099f-292">Size validation</span></span>

<span data-ttu-id="5099f-293">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="5099f-294">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="5099f-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="5099f-295">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="5099f-296">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="5099f-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="5099f-297">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="5099f-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="5099f-298">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="5099f-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="5099f-299">在 Razor 发布窗体数据或直接使用 JavaScript 的非窗体中 `FormData` ，在窗体的元素中指定的名称或 `FormData` 必须与控制器的操作中参数的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="5099f-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="5099f-300">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5099f-300">In the following example:</span></span>

* <span data-ttu-id="5099f-301">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="5099f-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="5099f-302">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="5099f-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="5099f-303">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="5099f-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="5099f-304">对于名为的页 Razor 页面处理程序方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="5099f-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="5099f-305">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="5099f-306">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="5099f-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="5099f-307">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="5099f-307">Multipart body length limit</span></span>

<span data-ttu-id="5099f-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="5099f-309">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="5099f-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="5099f-310">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="5099f-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="5099f-311">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="5099f-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="5099f-312">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="5099f-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="5099f-313">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5099f-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="5099f-314">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="5099f-315">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="5099f-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="5099f-316">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="5099f-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="5099f-317">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="5099f-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="5099f-318">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="5099f-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="5099f-319">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5099f-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="5099f-320">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="5099f-321">`RequestSizeLimitAttribute`还可以使用 [`@attribute`](xref:mvc/views/razor#attribute) Razor 指令应用：</span><span class="sxs-lookup"><span data-stu-id="5099f-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="5099f-322">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="5099f-322">Other Kestrel limits</span></span>

<span data-ttu-id="5099f-323">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="5099f-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="5099f-324">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="5099f-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="5099f-325">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="5099f-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="5099f-326">IIS 内容长度限制</span><span class="sxs-lookup"><span data-stu-id="5099f-326">IIS content length limit</span></span>

<span data-ttu-id="5099f-327">默认的请求限制 (`maxAllowedContentLength`) 为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="5099f-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="5099f-328">请在 web.config 文件中自定义此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-328">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="5099f-329">此设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="5099f-329">This setting only applies to IIS.</span></span> <span data-ttu-id="5099f-330">在 Kestrel 上托管时，默认情况下不会出现此行为。</span><span class="sxs-lookup"><span data-stu-id="5099f-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="5099f-331">有关详细信息，请参阅[请求 \<requestLimits> 限制](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="5099f-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="5099f-332">ASP.NET Core 模块中的限制或 IIS 请求筛选模块的存在可能会将上传限制在 2 或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="5099f-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="5099f-333">有关详细信息，请参阅[无法上传大小超出 2 GB 的文件 (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="5099f-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5099f-334">疑难解答</span><span class="sxs-lookup"><span data-stu-id="5099f-334">Troubleshoot</span></span>

<span data-ttu-id="5099f-335">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="5099f-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="5099f-336">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="5099f-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="5099f-337">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="5099f-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="5099f-338">有关提高此限制的详细信息，请参阅 [IIS 内容长度限制](#iis-content-length-limit)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="5099f-339">连接失败</span><span class="sxs-lookup"><span data-stu-id="5099f-339">Connection failure</span></span>

<span data-ttu-id="5099f-340">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="5099f-341">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="5099f-342">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="5099f-343">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="5099f-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="5099f-344">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="5099f-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="5099f-345">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="5099f-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="5099f-346">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="5099f-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="5099f-347">数据流太长</span><span class="sxs-lookup"><span data-stu-id="5099f-347">Stream was too long</span></span>

<span data-ttu-id="5099f-348">本主题中的示例依赖于 <xref:System.IO.MemoryStream> 来保存已上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="5099f-349">`MemoryStream` 的大小限制为 `int.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="5099f-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="5099f-350">如果应用的文件上传方案要求保存大于 50 MB 的文件内容，请使用另一种方法，该方法不依赖单个 `MemoryStream` 来保存已上传文件的内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5099f-351">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="5099f-352">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5099f-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="5099f-353">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="5099f-353">Security considerations</span></span>

<span data-ttu-id="5099f-354">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="5099f-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="5099f-355">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5099f-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="5099f-356">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="5099f-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="5099f-357">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="5099f-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="5099f-358">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="5099f-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="5099f-359">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="5099f-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="5099f-360">将文件上传到专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="5099f-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="5099f-361">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="5099f-362">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="5099f-363">请勿将上传的文件保存在与应用相同的目录树中\*\*\*\*。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="5099f-364">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="5099f-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="5099f-365">请勿使用用户提供的文件名或上载文件的不受信任的文件名。 &dagger;显示时，HTML 对不受信任的文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="5099f-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="5099f-366">例如，记录文件名或在 UI 中显示（自动对 Razor 输出进行 HTML 编码）。</span><span class="sxs-lookup"><span data-stu-id="5099f-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="5099f-367">仅允许应用设计规范的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="5099f-368">验证是否在服务器上执行了客户端检查。 &dagger;客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="5099f-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="5099f-369">检查已上传文件的大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="5099f-370">设置大小上限以防止上传大型文件。&dagger;</span><span class="sxs-lookup"><span data-stu-id="5099f-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="5099f-371">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="5099f-372">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="5099f-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="5099f-373">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="5099f-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-374">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="5099f-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="5099f-375">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="5099f-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="5099f-376">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="5099f-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="5099f-377">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="5099f-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="5099f-378">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="5099f-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="5099f-379">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="5099f-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="5099f-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="5099f-380">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="5099f-381">Azure 安全性：确保在接受用户文件时采取适当的控制措施</span><span class="sxs-lookup"><span data-stu-id="5099f-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="5099f-382">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="5099f-383">存储方案</span><span class="sxs-lookup"><span data-stu-id="5099f-383">Storage scenarios</span></span>

<span data-ttu-id="5099f-384">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="5099f-384">Common storage options for files include:</span></span>

* <span data-ttu-id="5099f-385">数据库</span><span class="sxs-lookup"><span data-stu-id="5099f-385">Database</span></span>

  * <span data-ttu-id="5099f-386">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="5099f-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="5099f-387">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="5099f-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="5099f-388">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="5099f-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="5099f-389">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="5099f-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="5099f-390">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="5099f-390">For large file uploads:</span></span>
    * <span data-ttu-id="5099f-391">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="5099f-392">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="5099f-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="5099f-393">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="5099f-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="5099f-394">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="5099f-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="5099f-395">切勿授予执行权限。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="5099f-396">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="5099f-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="5099f-397">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="5099f-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="5099f-398">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="5099f-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="5099f-399">有关详细信息，请参阅[快速入门：使用 .net 在对象存储中创建 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="5099f-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="5099f-400">此主题说明了 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>，但在处理 <xref:System.IO.Stream> 时，可以使用 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 将 <xref:System.IO.FileStream> 保存到 blob 存储。</span><span class="sxs-lookup"><span data-stu-id="5099f-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="5099f-401">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="5099f-401">File upload scenarios</span></span>

<span data-ttu-id="5099f-402">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="5099f-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="5099f-403">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="5099f-403">**Buffering**</span></span>

<span data-ttu-id="5099f-404">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="5099f-405">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="5099f-406">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="5099f-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="5099f-407">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="5099f-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="5099f-408">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="5099f-409">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="5099f-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="5099f-410">物理存储</span><span class="sxs-lookup"><span data-stu-id="5099f-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="5099f-411">Database</span><span class="sxs-lookup"><span data-stu-id="5099f-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="5099f-412">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="5099f-412">**Streaming**</span></span>

<span data-ttu-id="5099f-413">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="5099f-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="5099f-414">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="5099f-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="5099f-415">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="5099f-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="5099f-416">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="5099f-417">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="5099f-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="5099f-418">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="5099f-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="5099f-419">下面的示例演示 Razor 如何使用页面窗体上传一个文件（示例应用中的*Pages/BufferedSingleFileUploadPhysical* ）：</span><span class="sxs-lookup"><span data-stu-id="5099f-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="5099f-420">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="5099f-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="5099f-421">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="5099f-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="5099f-422">无验证。</span><span class="sxs-lookup"><span data-stu-id="5099f-422">There's no validation.</span></span>

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

<span data-ttu-id="5099f-423">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="5099f-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="5099f-424">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="5099f-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="5099f-425">改用 `XMLHttpRequest`</span><span class="sxs-lookup"><span data-stu-id="5099f-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="5099f-426">例如：</span><span class="sxs-lookup"><span data-stu-id="5099f-426">For example:</span></span>

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

<span data-ttu-id="5099f-427">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="5099f-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="5099f-428">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="5099f-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="5099f-429">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="5099f-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="5099f-430">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="5099f-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="5099f-431">除了显示和日志记录用途外，请勿使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="5099f-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="5099f-432">显示或日志记录时，HTML 对文件名进行编码。</span><span class="sxs-lookup"><span data-stu-id="5099f-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="5099f-433">攻击者可以提供恶意文件名，包括完整路径或相对路径。</span><span class="sxs-lookup"><span data-stu-id="5099f-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="5099f-434">应用程序应：</span><span class="sxs-lookup"><span data-stu-id="5099f-434">Applications should:</span></span>
>
> * <span data-ttu-id="5099f-435">从用户提供的文件名中删除路径。</span><span class="sxs-lookup"><span data-stu-id="5099f-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="5099f-436">为 UI 或日志记录保存经 HTML 编码、已删除路径的文件名。</span><span class="sxs-lookup"><span data-stu-id="5099f-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="5099f-437">生成新的随机文件名进行存储。</span><span class="sxs-lookup"><span data-stu-id="5099f-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="5099f-438">以下代码可从文件名中删除路径：</span><span class="sxs-lookup"><span data-stu-id="5099f-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="5099f-439">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="5099f-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="5099f-440">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="5099f-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5099f-441">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="5099f-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5099f-442">验证</span><span class="sxs-lookup"><span data-stu-id="5099f-442">Validation</span></span>](#validation)

<span data-ttu-id="5099f-443">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="5099f-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="5099f-444">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="5099f-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="5099f-445">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="5099f-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="5099f-446">[成员列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="5099f-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="5099f-447">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-447">Binding matches form files by name.</span></span> <span data-ttu-id="5099f-448">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="5099f-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="5099f-449">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="5099f-450">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5099f-450">The following example:</span></span>

* <span data-ttu-id="5099f-451">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="5099f-452">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="5099f-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="5099f-453">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="5099f-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="5099f-454">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="5099f-455">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="5099f-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="5099f-456">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="5099f-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="5099f-457">传递到  的路径必须包含文件名<xref:System.IO.FileStream> \*\*。</span><span class="sxs-lookup"><span data-stu-id="5099f-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="5099f-458">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="5099f-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="5099f-459">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="5099f-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="5099f-460">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="5099f-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="5099f-461">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="5099f-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="5099f-462">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-463">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="5099f-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="5099f-464">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="5099f-465">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="5099f-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="5099f-466">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="5099f-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="5099f-467">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="5099f-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="5099f-468">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="5099f-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="5099f-469">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="5099f-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="5099f-470"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="5099f-471">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="5099f-472">`FileUpload`在 Razor 页面窗体中使用：</span><span class="sxs-lookup"><span data-stu-id="5099f-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="5099f-473">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="5099f-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="5099f-474">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="5099f-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="5099f-475">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="5099f-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="5099f-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5099f-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="5099f-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="5099f-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-478">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="5099f-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="5099f-479">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="5099f-480">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="5099f-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="5099f-481">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="5099f-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="5099f-482">以下各节及[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="5099f-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="5099f-483">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="5099f-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="5099f-484">验证</span><span class="sxs-lookup"><span data-stu-id="5099f-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="5099f-485">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="5099f-485">Upload large files with streaming</span></span>

<span data-ttu-id="5099f-486">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5099f-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="5099f-487">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="5099f-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="5099f-488">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="5099f-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="5099f-489">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="5099f-490">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="5099f-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="5099f-491">初始页响应加载窗体并将防伪令牌保存在 Cookie 中（通过 `GenerateAntiforgeryTokenCookieAttribute` 属性）。</span><span class="sxs-lookup"><span data-stu-id="5099f-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="5099f-492">该属性使用 ASP.NET Core 的内置[防伪支持](xref:security/anti-request-forgery)来设置带有请求令牌的 cookie：</span><span class="sxs-lookup"><span data-stu-id="5099f-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="5099f-493">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="5099f-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="5099f-494">在示例应用中， `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 页面约定](xref:razor-pages/razor-pages-conventions)作为筛选器应用于和的页面应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="5099f-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="5099f-495">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="5099f-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="5099f-496">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="5099f-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="5099f-497">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="5099f-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="5099f-498">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="5099f-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="5099f-499">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="5099f-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="5099f-500">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="5099f-501">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)：\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="5099f-502">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="5099f-503">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="5099f-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="5099f-504">验证</span><span class="sxs-lookup"><span data-stu-id="5099f-504">Validation</span></span>

<span data-ttu-id="5099f-505">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="5099f-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="5099f-506">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="5099f-507">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="5099f-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="5099f-508">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="5099f-509">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="5099f-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="5099f-510">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="5099f-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="5099f-511">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="5099f-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="5099f-512">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="5099f-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="5099f-513">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="5099f-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="5099f-514">内容验证</span><span class="sxs-lookup"><span data-stu-id="5099f-514">Content validation</span></span>

<span data-ttu-id="5099f-515">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="5099f-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="5099f-516">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="5099f-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="5099f-517">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="5099f-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="5099f-518">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="5099f-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="5099f-519">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="5099f-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="5099f-520">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="5099f-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="5099f-521">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="5099f-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="5099f-522">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="5099f-522">File extension validation</span></span>

<span data-ttu-id="5099f-523">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="5099f-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="5099f-524">例如：</span><span class="sxs-lookup"><span data-stu-id="5099f-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="5099f-525">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="5099f-525">File signature validation</span></span>

<span data-ttu-id="5099f-526">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="5099f-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="5099f-527">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="5099f-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="5099f-528">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="5099f-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="5099f-529">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="5099f-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="5099f-530">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="5099f-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="5099f-531">文件名安全</span><span class="sxs-lookup"><span data-stu-id="5099f-531">File name security</span></span>

<span data-ttu-id="5099f-532">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="5099f-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="5099f-533">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="5099f-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="5099f-534">自动对属性值进行 HTML 编码以便显示。</span><span class="sxs-lookup"><span data-stu-id="5099f-534"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="5099f-535">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="5099f-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="5099f-536">在之外 Razor ，始终 <xref:System.Net.WebUtility.HtmlEncode*> 根据用户的请求来命名内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="5099f-537">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="5099f-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="5099f-538">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="5099f-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="5099f-539">大小验证</span><span class="sxs-lookup"><span data-stu-id="5099f-539">Size validation</span></span>

<span data-ttu-id="5099f-540">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="5099f-541">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="5099f-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="5099f-542">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="5099f-543">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="5099f-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="5099f-544">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="5099f-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="5099f-545">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="5099f-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="5099f-546">在 Razor 发布窗体数据或直接使用 JavaScript 的非窗体中 `FormData` ，在窗体的元素中指定的名称或 `FormData` 必须与控制器的操作中参数的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="5099f-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="5099f-547">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="5099f-547">In the following example:</span></span>

* <span data-ttu-id="5099f-548">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="5099f-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="5099f-549">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="5099f-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="5099f-550">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="5099f-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="5099f-551">对于名为的页 Razor 页面处理程序方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="5099f-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="5099f-552">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="5099f-553">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="5099f-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="5099f-554">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="5099f-554">Multipart body length limit</span></span>

<span data-ttu-id="5099f-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="5099f-556">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="5099f-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="5099f-557">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="5099f-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="5099f-558">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="5099f-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="5099f-559">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="5099f-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="5099f-560">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5099f-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="5099f-561">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="5099f-562">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="5099f-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="5099f-563">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="5099f-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="5099f-564">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="5099f-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="5099f-565">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="5099f-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="5099f-566">在 Razor 页面应用中，将筛选器应用于中的[约定](xref:razor-pages/razor-pages-conventions) `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5099f-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="5099f-567">在 Razor 页面应用或 MVC 应用中，将筛选器应用于页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="5099f-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="5099f-568">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="5099f-568">Other Kestrel limits</span></span>

<span data-ttu-id="5099f-569">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="5099f-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="5099f-570">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="5099f-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="5099f-571">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="5099f-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="5099f-572">IIS 内容长度限制</span><span class="sxs-lookup"><span data-stu-id="5099f-572">IIS content length limit</span></span>

<span data-ttu-id="5099f-573">默认的请求限制 (`maxAllowedContentLength`) 为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="5099f-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="5099f-574">请在 web.config 文件中自定义此限制：\*\*</span><span class="sxs-lookup"><span data-stu-id="5099f-574">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="5099f-575">此设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="5099f-575">This setting only applies to IIS.</span></span> <span data-ttu-id="5099f-576">在 Kestrel 上托管时，默认情况下不会出现此行为。</span><span class="sxs-lookup"><span data-stu-id="5099f-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="5099f-577">有关详细信息，请参阅[请求 \<requestLimits> 限制](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="5099f-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="5099f-578">ASP.NET Core 模块中的限制或 IIS 请求筛选模块的存在可能会将上传限制在 2 或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="5099f-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="5099f-579">有关详细信息，请参阅[无法上传大小超出 2 GB 的文件 (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="5099f-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5099f-580">疑难解答</span><span class="sxs-lookup"><span data-stu-id="5099f-580">Troubleshoot</span></span>

<span data-ttu-id="5099f-581">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="5099f-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="5099f-582">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="5099f-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="5099f-583">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="5099f-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="5099f-584">有关提高此限制的详细信息，请参阅 [IIS 内容长度限制](#iis-content-length-limit)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="5099f-585">连接失败</span><span class="sxs-lookup"><span data-stu-id="5099f-585">Connection failure</span></span>

<span data-ttu-id="5099f-586">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="5099f-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="5099f-587">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="5099f-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="5099f-588">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="5099f-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="5099f-589">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="5099f-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="5099f-590">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="5099f-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="5099f-591">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="5099f-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="5099f-592">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="5099f-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="5099f-593">数据流太长</span><span class="sxs-lookup"><span data-stu-id="5099f-593">Stream was too long</span></span>

<span data-ttu-id="5099f-594">本主题中的示例依赖于 <xref:System.IO.MemoryStream> 来保存已上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="5099f-595">`MemoryStream` 的大小限制为 `int.MaxValue`。</span><span class="sxs-lookup"><span data-stu-id="5099f-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="5099f-596">如果应用的文件上传方案要求保存大于 50 MB 的文件内容，请使用另一种方法，该方法不依赖单个 `MemoryStream` 来保存已上传文件的内容。</span><span class="sxs-lookup"><span data-stu-id="5099f-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="5099f-597">其他资源</span><span class="sxs-lookup"><span data-stu-id="5099f-597">Additional resources</span></span>

* [<span data-ttu-id="5099f-598">HTTP 连接请求排出</span><span class="sxs-lookup"><span data-stu-id="5099f-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="5099f-599">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="5099f-599">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="5099f-600">Azure 安全：安全框架：输入验证 |措施</span><span class="sxs-lookup"><span data-stu-id="5099f-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="5099f-601">Azure 云设计模式：附属密钥模式</span><span class="sxs-lookup"><span data-stu-id="5099f-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
