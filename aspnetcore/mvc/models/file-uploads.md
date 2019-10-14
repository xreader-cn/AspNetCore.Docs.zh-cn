---
title: 在 ASP.NET Core 中上传文件
author: guardrex
description: 如何在 ASP.NET Core MVC 中使用模型绑定和流式处理上传文件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: de8bfee22e39dfc5a6ed254cf0555887891d4590
ms.sourcegitcommit: d81912782a8b0bd164f30a516ad80f8defb5d020
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179309"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="f3b1a-103">在 ASP.NET Core 中上传文件</span><span class="sxs-lookup"><span data-stu-id="f3b1a-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="f3b1a-104">作者：[Luke Latham](https://github.com/guardrex)、[Steve Smith](https://ardalis.com/) 和 [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="f3b1a-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3b1a-105">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f3b1a-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f3b1a-107">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f3b1a-107">Security considerations</span></span>

<span data-ttu-id="f3b1a-108">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f3b1a-109">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="f3b1a-110">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f3b1a-111">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="f3b1a-112">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f3b1a-113">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f3b1a-114">将文件上传到系统的专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-114">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="f3b1a-115">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-115">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f3b1a-116">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f3b1a-117">切勿将上传的文件保存在与应用相同的目录树中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-117">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f3b1a-118">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f3b1a-119">请勿使用用户提供的文件名，或上传的文件的不受信任的文件名。&dagger;若要在 UI 或登录消息中显示不受信任的文件名，请对值进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="f3b1a-120">仅允许使用一组特定的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-120">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="f3b1a-121">检查文件格式签名，阻止用户上传伪装文件。&dagger;例如，请勿允许用户上传带 .txt 扩展名的 .exe 文件。  </span><span class="sxs-lookup"><span data-stu-id="f3b1a-121">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="f3b1a-122">验证是否也在服务器上执行了客户端检查。&dagger;客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-122">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f3b1a-123">检查上传的文件的大小，阻止超出预期大小的文件上传。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-123">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="f3b1a-124">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-124">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f3b1a-125">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-125">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f3b1a-126">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-126">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-127">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-127">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f3b1a-128">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-128">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f3b1a-129">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-129">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f3b1a-130">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-130">Compromise user or system data.</span></span>
> * <span data-ttu-id="f3b1a-131">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-131">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f3b1a-132">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-132">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="f3b1a-133">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-133">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="f3b1a-134">Azure 安全性：确保接受用户的文件时实施适当的控制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-134">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f3b1a-135">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-135">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f3b1a-136">存储方案</span><span class="sxs-lookup"><span data-stu-id="f3b1a-136">Storage scenarios</span></span>

<span data-ttu-id="f3b1a-137">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-137">Common storage options for files include:</span></span>

* <span data-ttu-id="f3b1a-138">数据库</span><span class="sxs-lookup"><span data-stu-id="f3b1a-138">Database</span></span>

  * <span data-ttu-id="f3b1a-139">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-139">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f3b1a-140">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-140">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f3b1a-141">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-141">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f3b1a-142">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-142">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f3b1a-143">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-143">For large file uploads:</span></span>
    * <span data-ttu-id="f3b1a-144">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-144">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f3b1a-145">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-145">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f3b1a-146">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-146">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f3b1a-147">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-147">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f3b1a-148">切勿授予执行权限。 </span><span class="sxs-lookup"><span data-stu-id="f3b1a-148">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f3b1a-149">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-149">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f3b1a-150">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-150">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f3b1a-151">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-151">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f3b1a-152">有关详细信息，请参阅[快速入门：使用 .NET 在对象存储中创建 Blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-152">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="f3b1a-153">此主题说明了 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>，但在处理 <xref:System.IO.Stream> 时，可以使用 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 将 <xref:System.IO.FileStream> 保存到 blob 存储。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-153">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f3b1a-154">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="f3b1a-154">File upload scenarios</span></span>

<span data-ttu-id="f3b1a-155">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f3b1a-156">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-156">**Buffering**</span></span>

<span data-ttu-id="f3b1a-157">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f3b1a-158">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f3b1a-159">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f3b1a-160">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f3b1a-161">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f3b1a-162">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f3b1a-163">物理存储</span><span class="sxs-lookup"><span data-stu-id="f3b1a-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f3b1a-164">数据库</span><span class="sxs-lookup"><span data-stu-id="f3b1a-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f3b1a-165">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-165">**Streaming**</span></span>

<span data-ttu-id="f3b1a-166">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f3b1a-167">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f3b1a-168">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f3b1a-169">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f3b1a-170">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="f3b1a-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f3b1a-171">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f3b1a-172">下面的示例演示了使用 Razor Pages 窗体上传单个文件（示例应用中的 Pages/BufferedSingleFileUploadPhysical.cshtml  ）：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f3b1a-173">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f3b1a-174">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f3b1a-175">无验证。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-175">There's no validation.</span></span>

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

<span data-ttu-id="f3b1a-176">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f3b1a-177">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f3b1a-178">请使用 `XMLHttpRequest`。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f3b1a-179">例如:</span><span class="sxs-lookup"><span data-stu-id="f3b1a-179">For example:</span></span>

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

<span data-ttu-id="f3b1a-180">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f3b1a-181">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f3b1a-182">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f3b1a-183">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-184">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-184">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f3b1a-185">只应将 `FileName` 属性用于显示用途，并且只应在对值进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-185">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="f3b1a-186">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-186">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f3b1a-187">以下各节及[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-187">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f3b1a-188">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f3b1a-188">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f3b1a-189">验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-189">Validation</span></span>](#validation)

<span data-ttu-id="f3b1a-190">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-190">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f3b1a-191">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-191">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f3b1a-192">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-192">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f3b1a-193">[列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f3b1a-193">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f3b1a-194">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-194">Binding matches form files by name.</span></span> <span data-ttu-id="f3b1a-195">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-195">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f3b1a-196">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-196">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f3b1a-197">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-197">The following example:</span></span>

* <span data-ttu-id="f3b1a-198">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-198">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f3b1a-199">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-199">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f3b1a-200">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-200">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f3b1a-201">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-201">Returns the total number and size of files uploaded.</span></span>

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

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="f3b1a-202">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-202">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f3b1a-203">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-203">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f3b1a-204">传递到 <xref:System.IO.FileStream> 的路径必须包含文件名  。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-204">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f3b1a-205">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-205">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f3b1a-206">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-206">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f3b1a-207">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-207">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f3b1a-208">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-208">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f3b1a-209">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。 </span><span class="sxs-lookup"><span data-stu-id="f3b1a-209">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-210">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-210">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f3b1a-211">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-211">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f3b1a-212">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-212">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f3b1a-213">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="f3b1a-213">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f3b1a-214">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="f3b1a-214">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f3b1a-215">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-215">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f3b1a-216">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-216">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f3b1a-217"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-217"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f3b1a-218">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-218">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f3b1a-219">在 Razor Pages 窗体中使用 `FileUpload`：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-219">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f3b1a-220">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-220">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f3b1a-221">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-221">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f3b1a-222">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-222">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f3b1a-223">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f3b1a-223">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f3b1a-224">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f3b1a-224">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-225">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-225">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f3b1a-226">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-226">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f3b1a-227">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-227">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f3b1a-228">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-228">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f3b1a-229">以下各节及[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-229">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f3b1a-230">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f3b1a-230">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f3b1a-231">验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-231">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f3b1a-232">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="f3b1a-232">Upload large files with streaming</span></span>

<span data-ttu-id="f3b1a-233">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-233">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f3b1a-234">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-234">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f3b1a-235">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-235">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f3b1a-236">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-236">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f3b1a-237">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-237">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f3b1a-238">初始页响应加载窗体并将防伪令牌保存在 Cookie 中（通过 `GenerateAntiforgeryTokenCookieAttribute` 属性）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-238">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f3b1a-239">该属性使用 ASP.NET Core 的内置[防伪支持](xref:security/anti-request-forgery)来设置包含请求令牌的 Cookie：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-239">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f3b1a-240">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-240">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f3b1a-241">在示例应用的 `Startup.ConfigureServices` 中，使用 [Razor Pages 约定](xref:razor-pages/razor-pages-conventions)将 `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` 作为筛选器应用到 `/StreamedSingleFileUploadDb` 和 `/StreamedSingleFileUploadPhysical` 的页应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-241">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="f3b1a-242">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-242">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f3b1a-243">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-243">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f3b1a-244">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-244">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f3b1a-245">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-245">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f3b1a-246">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-246">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f3b1a-247">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-247">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f3b1a-248">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)： </span><span class="sxs-lookup"><span data-stu-id="f3b1a-248">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f3b1a-249">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-249">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f3b1a-250">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-250">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f3b1a-251">验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-251">Validation</span></span>

<span data-ttu-id="f3b1a-252">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-252">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f3b1a-253">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。 </span><span class="sxs-lookup"><span data-stu-id="f3b1a-253">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f3b1a-254">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-254">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-255">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-255">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f3b1a-256">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-256">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f3b1a-257">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-257">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f3b1a-258">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-258">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f3b1a-259">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-259">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f3b1a-260">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-260">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f3b1a-261">内容验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-261">Content validation</span></span>

<span data-ttu-id="f3b1a-262">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-262">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f3b1a-263">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-263">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f3b1a-264">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-264">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f3b1a-265">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-265">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f3b1a-266">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-266">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f3b1a-267">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-267">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f3b1a-268">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-268">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f3b1a-269">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-269">File extension validation</span></span>

<span data-ttu-id="f3b1a-270">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-270">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f3b1a-271">例如:</span><span class="sxs-lookup"><span data-stu-id="f3b1a-271">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f3b1a-272">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-272">File signature validation</span></span>

<span data-ttu-id="f3b1a-273">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-273">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f3b1a-274">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-274">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f3b1a-275">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-275">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f3b1a-276">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-276">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f3b1a-277">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-277">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f3b1a-278">文件名安全</span><span class="sxs-lookup"><span data-stu-id="f3b1a-278">File name security</span></span>

<span data-ttu-id="f3b1a-279">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-279">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f3b1a-280">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-280">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f3b1a-281">Razor 自动对属性值执行 HTML 编码，以便于显示。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-281">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f3b1a-282">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-282">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f3b1a-283">在 Razor 外部，始终对用户请求中的文件名内容执行 <xref:System.Net.WebUtility.HtmlEncode*>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-283">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f3b1a-284">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-284">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f3b1a-285">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-285">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f3b1a-286">大小验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-286">Size validation</span></span>

<span data-ttu-id="f3b1a-287">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-287">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f3b1a-288">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-288">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f3b1a-289">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制： </span><span class="sxs-lookup"><span data-stu-id="f3b1a-289">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f3b1a-290">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-290">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f3b1a-291">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-291">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f3b1a-292">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="f3b1a-292">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f3b1a-293">在发布窗体数据或直接使用 JavaScript `FormData` 的非 Razor 窗体中，窗体元素或 `FormData` 指定的名称必须与控制器操作的参数名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-293">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f3b1a-294">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-294">In the following example:</span></span>

* <span data-ttu-id="f3b1a-295">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-295">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f3b1a-296">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-296">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f3b1a-297">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-297">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f3b1a-298">对于名称为 `Upload` 的 Razor Pages 页处理程序方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-298">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f3b1a-299">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-299">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f3b1a-300">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="f3b1a-300">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f3b1a-301">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-301">Multipart body length limit</span></span>

<span data-ttu-id="f3b1a-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f3b1a-303">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-303">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f3b1a-304">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-304">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f3b1a-305">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-305">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f3b1a-306">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-306"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f3b1a-307">在 Razor Pages 应用中，使用 `Startup.ConfigureServices` 中的[约定](xref:razor-pages/razor-pages-conventions)应用筛选器：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-307">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="f3b1a-308">在 Razor Pages 应用或 MVC 应用中，将筛选器应用到页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-308">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f3b1a-309">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="f3b1a-309">Kestrel maximum request body size</span></span>

<span data-ttu-id="f3b1a-310">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-310">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f3b1a-311">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-311">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="f3b1a-312">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-312"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f3b1a-313">在 Razor Pages 应用中，使用 `Startup.ConfigureServices` 中的[约定](xref:razor-pages/razor-pages-conventions)应用筛选器：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
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
    });
```

<span data-ttu-id="f3b1a-314">在 Razor Pages 应用或 MVC 应用中，将筛选器应用到页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-314">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="f3b1a-315">也可以使用 [@attribute](xref:mvc/views/razor#attribute) Razor 指令应用 `RequestSizeLimitAttribute`：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-315">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f3b1a-316">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-316">Other Kestrel limits</span></span>

<span data-ttu-id="f3b1a-317">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-317">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f3b1a-318">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="f3b1a-318">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f3b1a-319">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="f3b1a-319">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f3b1a-320">IIS 内容长度限制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-320">IIS content length limit</span></span>

<span data-ttu-id="f3b1a-321">默认的请求限制 (`maxAllowedContentLength`) 为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-321">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f3b1a-322">请在 web.config 文件中自定义此限制： </span><span class="sxs-lookup"><span data-stu-id="f3b1a-322">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f3b1a-323">此设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-323">This setting only applies to IIS.</span></span> <span data-ttu-id="f3b1a-324">在 Kestrel 上托管时，默认情况下不会出现此行为。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-324">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f3b1a-325">有关详细信息，请参阅[请求限制 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-325">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f3b1a-326">ASP.NET Core 模块中的限制或 IIS 请求筛选模块的存在可能会将上传限制在 2 或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-326">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f3b1a-327">有关详细信息，请参阅[无法上传大小超出 2 GB 的文件 (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-327">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f3b1a-328">疑难解答</span><span class="sxs-lookup"><span data-stu-id="f3b1a-328">Troubleshoot</span></span>

<span data-ttu-id="f3b1a-329">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-329">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f3b1a-330">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="f3b1a-330">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f3b1a-331">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-331">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f3b1a-332">有关提高此限制的详细信息，请参阅 [IIS 内容长度限制](#iis-content-length-limit)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-332">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f3b1a-333">连接失败</span><span class="sxs-lookup"><span data-stu-id="f3b1a-333">Connection failure</span></span>

<span data-ttu-id="f3b1a-334">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-334">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f3b1a-335">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-335">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f3b1a-336">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-336">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f3b1a-337">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="f3b1a-337">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f3b1a-338">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-338">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f3b1a-339">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-339">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f3b1a-340">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-340">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3b1a-341">ASP.NET Core 支持使用缓冲的模型绑定（针对较小文件）和无缓冲的流式传输（针对较大文件）上传一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-341">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f3b1a-342">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-342">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f3b1a-343">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f3b1a-343">Security considerations</span></span>

<span data-ttu-id="f3b1a-344">向用户提供向服务器上传文件的功能时，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-344">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f3b1a-345">攻击者可能会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-345">Attackers may attempt to:</span></span>

* <span data-ttu-id="f3b1a-346">执行[拒绝服务](/windows-hardware/drivers/ifs/denial-of-service)攻击。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-346">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f3b1a-347">上传病毒或恶意软件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-347">Upload viruses or malware.</span></span>
* <span data-ttu-id="f3b1a-348">以其他方式破坏网络和服务器。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-348">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f3b1a-349">降低成功攻击可能性的安全措施如下：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-349">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f3b1a-350">将文件上传到系统的专用文件上传区域，最好是非系统驱动器。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-350">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="f3b1a-351">使用专用位置便于对上传的文件实施安全限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-351">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f3b1a-352">禁用对文件上传位置的执行权限。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-352">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f3b1a-353">切勿将上传的文件保存在与应用相同的目录树中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-353">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f3b1a-354">使用应用确定的安全的文件名。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-354">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f3b1a-355">请勿使用用户提供的文件名，或上传的文件的不受信任的文件名。&dagger;若要在 UI 或登录消息中显示不受信任的文件名，请对值进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-355">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="f3b1a-356">仅允许使用一组特定的已批准文件扩展名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-356">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="f3b1a-357">检查文件格式签名，阻止用户上传伪装文件。&dagger;例如，请勿允许用户上传带 .txt 扩展名的 .exe 文件。  </span><span class="sxs-lookup"><span data-stu-id="f3b1a-357">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="f3b1a-358">验证是否也在服务器上执行了客户端检查。&dagger;客户端检查很容易规避。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-358">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f3b1a-359">检查上传的文件的大小，阻止超出预期大小的文件上传。&dagger;</span><span class="sxs-lookup"><span data-stu-id="f3b1a-359">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="f3b1a-360">文件不应该被具有相同名称的上传文件覆盖时，先在数据库或物理存储上检查文件名，然后再上传文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-360">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f3b1a-361">**先对上传的内容运行病毒/恶意软件扫描程序，然后再存储文件。**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-361">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f3b1a-362">&dagger;示例应用演示了符合条件的方法。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-362">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-363">将恶意代码上传到系统通常是执行代码的第一步，这些代码可以：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-363">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f3b1a-364">完全获得对系统的控制权限。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-364">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f3b1a-365">重载系统，导致系统崩溃。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-365">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f3b1a-366">泄露用户或系统数据。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-366">Compromise user or system data.</span></span>
> * <span data-ttu-id="f3b1a-367">将涂鸦应用于公共 UI。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-367">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f3b1a-368">有关在接受用户文件时减少攻击外围应用的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-368">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="f3b1a-369">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-369">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="f3b1a-370">Azure 安全性：确保接受用户的文件时实施适当的控制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-370">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f3b1a-371">有关实现安全措施（包括示例应用中的示例）的详细信息，请参阅[验证](#validation)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-371">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f3b1a-372">存储方案</span><span class="sxs-lookup"><span data-stu-id="f3b1a-372">Storage scenarios</span></span>

<span data-ttu-id="f3b1a-373">常见的文件存储选项有：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-373">Common storage options for files include:</span></span>

* <span data-ttu-id="f3b1a-374">数据库</span><span class="sxs-lookup"><span data-stu-id="f3b1a-374">Database</span></span>

  * <span data-ttu-id="f3b1a-375">对于小型文件上传，数据库通常快于物理存储（文件系统或网络共享）选项。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-375">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f3b1a-376">相对于物理存储选项，数据库通常更为便利，因为检索数据库记录来获取用户数据可同时提供文件内容（如头像图像）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-376">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f3b1a-377">相对于使用数据存储服务，数据库的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-377">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f3b1a-378">物理存储（文件系统或网络共享）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-378">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f3b1a-379">对于大型文件上传：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-379">For large file uploads:</span></span>
    * <span data-ttu-id="f3b1a-380">数据库限制可能会限制上传的大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-380">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f3b1a-381">相对于数据库存储，物理存储通常成本更高。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-381">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f3b1a-382">相对于使用数据存储服务，物理存储的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-382">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f3b1a-383">应用的进程必须具有存储位置的读写权限。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-383">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f3b1a-384">切勿授予执行权限。 </span><span class="sxs-lookup"><span data-stu-id="f3b1a-384">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f3b1a-385">数据存储服务（例如，[Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-385">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f3b1a-386">服务通常通过本地解决方案提供提升的可伸缩性和复原能力，而它们往往受单一故障点的影响。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-386">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f3b1a-387">在大型存储基础结构方案中，服务的成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-387">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f3b1a-388">有关详细信息，请参阅[快速入门：使用 .NET 在对象存储中创建 Blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-388">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="f3b1a-389">此主题说明了 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>，但在处理 <xref:System.IO.Stream> 时，可以使用 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 将 <xref:System.IO.FileStream> 保存到 blob 存储。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-389">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f3b1a-390">文件上传方案</span><span class="sxs-lookup"><span data-stu-id="f3b1a-390">File upload scenarios</span></span>

<span data-ttu-id="f3b1a-391">缓冲和流式传输是上传文件的两种常见方法。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-391">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f3b1a-392">**缓冲**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-392">**Buffering**</span></span>

<span data-ttu-id="f3b1a-393">整个文件读入 <xref:Microsoft.AspNetCore.Http.IFormFile>，它是文件的 C# 表示形式，用于处理或保存文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-393">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f3b1a-394">文件上传所用的资源（磁盘、内存）取决于并发文件上传的数量和大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-394">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f3b1a-395">如果应用尝试缓冲过多上传，站点就会在内存或磁盘空间不足时崩溃。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-395">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f3b1a-396">如果文件上传的大小或频率会消耗应用资源，请使用流式传输。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-396">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f3b1a-397">会将大于 64 KB 的所有单个缓冲文件从内存移到磁盘的临时文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-397">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f3b1a-398">本主题的以下部分介绍了如何缓冲小型文件：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-398">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f3b1a-399">物理存储</span><span class="sxs-lookup"><span data-stu-id="f3b1a-399">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f3b1a-400">数据库</span><span class="sxs-lookup"><span data-stu-id="f3b1a-400">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f3b1a-401">**流式处理**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-401">**Streaming**</span></span>

<span data-ttu-id="f3b1a-402">从多部分请求收到文件，然后应用直接处理或保存它。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-402">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f3b1a-403">流式传输无法显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-403">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f3b1a-404">流式传输可降低上传文件时对内存或磁盘空间的需求。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-404">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f3b1a-405">[通过流式传输上传大型文件](#upload-large-files-with-streaming)部分介绍了如何流式传输大型文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-405">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f3b1a-406">通过缓冲的模型绑定将小型文件上传到物理存储</span><span class="sxs-lookup"><span data-stu-id="f3b1a-406">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f3b1a-407">要上传小文件，请使用多部分窗体或使用 JavaScript 构造 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-407">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f3b1a-408">下面的示例演示了使用 Razor Pages 窗体上传单个文件（示例应用中的 Pages/BufferedSingleFileUploadPhysical.cshtml  ）：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-408">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f3b1a-409">下面的示例与前面的示例类似，不同之处在于：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-409">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f3b1a-410">使用 JavaScript ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 提交窗体的数据。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-410">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f3b1a-411">无验证。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-411">There's no validation.</span></span>

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

<span data-ttu-id="f3b1a-412">若要使用 JavaScript 为[不支持 Fetch API](https://caniuse.com/#feat=fetch) 的客户端执行窗体发布，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-412">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f3b1a-413">使用 Fetch Polyfill（例如，[window.fetch polyfill (github/fetch)](https://github.com/github/fetch)）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-413">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f3b1a-414">请使用 `XMLHttpRequest`。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-414">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f3b1a-415">例如:</span><span class="sxs-lookup"><span data-stu-id="f3b1a-415">For example:</span></span>

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

<span data-ttu-id="f3b1a-416">为支持文件上传，HTML 窗体必须指定 `multipart/form-data` 的编码类型 (`enctype`)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-416">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f3b1a-417">要使 `files` 输入元素支持上传多个文件，请在 `<input>` 元素上提供 `multiple` 属性：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-417">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f3b1a-418">上传到服务器的单个文件可使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 接口通过[模型绑定](xref:mvc/models/model-binding)进行访问。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-418">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f3b1a-419">示例应用演示了数据库和物理存储方案的多个缓冲文件上传。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-419">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-420">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-420">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f3b1a-421">只应将 `FileName` 属性用于显示用途，并且只应在对值进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-421">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="f3b1a-422">目前提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-422">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f3b1a-423">以下各节及[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-423">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f3b1a-424">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f3b1a-424">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f3b1a-425">验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-425">Validation</span></span>](#validation)

<span data-ttu-id="f3b1a-426">使用模型绑定和 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传文件时，操作方法可以接受以下内容：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-426">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f3b1a-427">单个 <xref:Microsoft.AspNetCore.Http.IFormFile>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-427">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f3b1a-428">以下任何表示多个文件的集合：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-428">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f3b1a-429">[列表](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f3b1a-429">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f3b1a-430">绑定根据名称匹配窗体文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-430">Binding matches form files by name.</span></span> <span data-ttu-id="f3b1a-431">例如，`<input type="file" name="formFile">` 中的 HTML `name` 值必须与 C# 参数/属性绑定 (`FormFile`) 匹配。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-431">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f3b1a-432">有关详细信息，请参阅[使名称属性值与 POST 方法的参数名匹配](#match-name-attribute-value-to-parameter-name-of-post-method)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-432">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f3b1a-433">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-433">The following example:</span></span>

* <span data-ttu-id="f3b1a-434">循环访问一个或多个上传的文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-434">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f3b1a-435">使用 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 返回文件的完整路径，包括文件名称。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-435">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f3b1a-436">使用应用生成的文件名将文件保存到本地文件系统。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-436">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f3b1a-437">返回上传的文件的总数量和总大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-437">Returns the total number and size of files uploaded.</span></span>

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

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="f3b1a-438">使用 `Path.GetRandomFileName` 生成文件名（不含路径）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-438">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f3b1a-439">在下面的示例中，从配置获取路径：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-439">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f3b1a-440">传递到 <xref:System.IO.FileStream> 的路径必须包含文件名  。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-440">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f3b1a-441">如果未提供文件名，则会在运行时引发 <xref:System.UnauthorizedAccessException>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-441">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f3b1a-442">使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 技术上传的文件在处理之前会缓冲在内存中或服务器的磁盘中。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-442">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f3b1a-443">在操作方法中，<xref:Microsoft.AspNetCore.Http.IFormFile> 内容可作为 <xref:System.IO.Stream> 访问。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-443">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f3b1a-444">除本地文件系统之外，还可以将文件保存到网络共享或文件存储服务，如 [Azure Blob 存储](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-444">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f3b1a-445">若要查看循环访问要上传的多个文件并且使用安全文件名的其他示例，请参阅示例应用中的 Pages/BufferedMultipleFileUploadPhysical.cshtml.cs。 </span><span class="sxs-lookup"><span data-stu-id="f3b1a-445">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-446">如果在未删除先前临时文件的情况下创建了 65,535 个以上的文件，则 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 将抛出一个 <xref:System.IO.IOException>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-446">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f3b1a-447">65,535 个文件限制是每个服务器的限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-447">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f3b1a-448">有关 Windows 操作系统上的此限制的详细信息，请参阅以下主题中的说明：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-448">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f3b1a-449">GetTempFileNameA 函数</span><span class="sxs-lookup"><span data-stu-id="f3b1a-449">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f3b1a-450">使用缓冲的模型绑定将小型文件上传到数据库</span><span class="sxs-lookup"><span data-stu-id="f3b1a-450">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f3b1a-451">要使用[实体框架](/ef/core/index)将二进制文件数据存储在数据库中，请在实体上定义 <xref:System.Byte> 数组属性：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-451">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f3b1a-452">为包括 <xref:Microsoft.AspNetCore.Http.IFormFile> 的类指定页模型属性：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-452">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f3b1a-453"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接用作操作方法参数或绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-453"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f3b1a-454">前面的示例使用绑定模型属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-454">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f3b1a-455">在 Razor Pages 窗体中使用 `FileUpload`：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-455">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f3b1a-456">将窗体发布到服务器后，将 <xref:Microsoft.AspNetCore.Http.IFormFile> 复制到流，并将它作为字节数组保存在数据库中。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-456">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f3b1a-457">在下面的示例中，`_dbContext` 存储应用的数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-457">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f3b1a-458">上面的示例与示例应用中演示的方案相似：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-458">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f3b1a-459">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f3b1a-459">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f3b1a-460">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f3b1a-460">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-461">在关系数据库中存储二进制数据时要格外小心，因为它可能对性能产生不利影响。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-461">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f3b1a-462">切勿依赖或信任未经验证的 <xref:Microsoft.AspNetCore.Http.IFormFile> 的 `FileName` 属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-462">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f3b1a-463">只应将 `FileName` 属性用于显示用途，并且只应在进行 HTML 编码后使用它。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-463">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f3b1a-464">提供的示例未考虑安全注意事项。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-464">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f3b1a-465">以下各节及[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)提供了其他信息：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-465">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f3b1a-466">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="f3b1a-466">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f3b1a-467">验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-467">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f3b1a-468">通过流式传输上传大型文件</span><span class="sxs-lookup"><span data-stu-id="f3b1a-468">Upload large files with streaming</span></span>

<span data-ttu-id="f3b1a-469">以下示例演示如何使用 JavaScript 将文件流式传输到控制器操作。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-469">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f3b1a-470">使用自定义筛选器属性生成文件的防伪令牌，并将其传递到客户端 HTTP 头中（而不是在请求正文中传递）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-470">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f3b1a-471">由于操作方法直接处理上传的数据，所以其他自定义筛选器会禁用窗体模型绑定。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-471">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f3b1a-472">在该操作中，使用 `MultipartReader` 读取窗体的内容，它会读取每个单独的 `MultipartSection`，从而根据需要处理文件或存储内容。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-472">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f3b1a-473">读取多部分节后，该操作会执行自己的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-473">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f3b1a-474">初始页响应加载窗体并将防伪令牌保存在 Cookie 中（通过 `GenerateAntiforgeryTokenCookieAttribute` 属性）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-474">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f3b1a-475">该属性使用 ASP.NET Core 的内置[防伪支持](xref:security/anti-request-forgery)来设置包含请求令牌的 Cookie：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-475">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f3b1a-476">使用 `DisableFormValueModelBindingAttribute` 禁用模型绑定：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-476">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f3b1a-477">在示例应用的 `Startup.ConfigureServices` 中，使用 [Razor Pages 约定](xref:razor-pages/razor-pages-conventions)将 `GenerateAntiforgeryTokenCookieAttribute` 和 `DisableFormValueModelBindingAttribute` 作为筛选器应用到 `/StreamedSingleFileUploadDb` 和 `/StreamedSingleFileUploadPhysical` 的页应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-477">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="f3b1a-478">由于模型绑定不读取窗体，因此不绑定从窗体绑定的参数（查询、路由和标头继续运行）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-478">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f3b1a-479">操作方法直接使用 `Request` 属性。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-479">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f3b1a-480">`MultipartReader` 用于读取每个节。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-480">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f3b1a-481">在 `KeyValueAccumulator` 中存储键值数据。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-481">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f3b1a-482">读取多部分节后，系统会使用 `KeyValueAccumulator` 的内容将窗体数据绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-482">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f3b1a-483">使用 EF Core 流式传输到数据库的完整 `StreamingController.UploadDatabase` 方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-483">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f3b1a-484">`MultipartRequestHelper` (Utilities/MultipartRequestHelper.cs)： </span><span class="sxs-lookup"><span data-stu-id="f3b1a-484">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f3b1a-485">流式传输到物理位置的完整 `StreamingController.UploadPhysical` 方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-485">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f3b1a-486">在示例应用中，由 `FileHelpers.ProcessStreamedFile` 处理验证检查。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-486">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f3b1a-487">验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-487">Validation</span></span>

<span data-ttu-id="f3b1a-488">示例应用的 `FileHelpers` 类演示对缓冲 <xref:Microsoft.AspNetCore.Http.IFormFile> 和流式传输文件上传的多项检查。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-488">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f3b1a-489">有关示例应用如何处理 <xref:Microsoft.AspNetCore.Http.IFormFile> 缓冲文件上传的信息，请参阅 Utilities/FileHelpers.cs 文件中的 `ProcessFormFile` 方法。 </span><span class="sxs-lookup"><span data-stu-id="f3b1a-489">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f3b1a-490">有关如何处理流式传输的文件的信息，请参阅同一个文件中的 `ProcessStreamedFile` 方法。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-490">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f3b1a-491">示例应用演示的验证处理方法不扫描上传的文件的内容。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-491">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f3b1a-492">在多数生产方案中，会先将病毒/恶意软件扫描程序 API 用于文件，然后再向用户或其他系统提供文件。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-492">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f3b1a-493">尽管主题示例提供了验证技巧工作示例，但是如果不满足以下情况，请勿在生产应用中实现 `FileHelpers` 类：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-493">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f3b1a-494">完全理解此实现。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-494">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f3b1a-495">根据应用的环境和规范修改实现。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-495">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f3b1a-496">**切勿未处理这些要求即随意在应用中实现安全代码。**</span><span class="sxs-lookup"><span data-stu-id="f3b1a-496">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f3b1a-497">内容验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-497">Content validation</span></span>

<span data-ttu-id="f3b1a-498">**将第三方病毒/恶意软件扫描 API 用于上传的内容**。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-498">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f3b1a-499">在大容量方案中，在服务器资源上扫描文件较为困难。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-499">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f3b1a-500">若文件扫描导致请求处理性能降低，请考虑将扫描工作卸载到[后台服务](xref:fundamentals/host/hosted-services)，该服务可以是在应用服务器之外的服务器上运行的服务。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-500">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f3b1a-501">通常会将卸载的文件保留在隔离区，直至后台病毒扫描程序检查它们。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-501">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f3b1a-502">文件通过检查时，会将相应的文件移到常规的文件存储位置。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-502">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f3b1a-503">通常在执行这些步骤的同时，会提供指示文件扫描状态的数据库记录。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-503">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f3b1a-504">通过此方法，应用和应用服务器可以持续以响应请求为重点。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-504">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f3b1a-505">文件扩展名验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-505">File extension validation</span></span>

<span data-ttu-id="f3b1a-506">应在允许的扩展名列表中查找上传的文件的扩展名。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-506">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f3b1a-507">例如:</span><span class="sxs-lookup"><span data-stu-id="f3b1a-507">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f3b1a-508">文件签名验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-508">File signature validation</span></span>

<span data-ttu-id="f3b1a-509">文件的签名由文件开头部分中的前几个字节确定。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-509">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f3b1a-510">可以使用这些字节指示扩展名是否与文件内容匹配。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-510">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f3b1a-511">示例应用检查一些常见文件类型的文件签名。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-511">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f3b1a-512">在下面的示例中，在文件上检查 JPEG 图像的文件签名：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-512">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f3b1a-513">若要获取其他文件签名，请参阅[文件签名数据库](https://www.filesignatures.net/)和官方文件规范。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-513">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f3b1a-514">文件名安全</span><span class="sxs-lookup"><span data-stu-id="f3b1a-514">File name security</span></span>

<span data-ttu-id="f3b1a-515">切勿使用客户端提供的文件名来将文件保存到物理存储。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-515">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f3b1a-516">使用 [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) 为文件创建安全的文件名，以创建完整路径（包括文件名）来执行临时存储。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-516">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f3b1a-517">Razor 自动对属性值执行 HTML 编码，以便于显示。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-517">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f3b1a-518">以下代码安全可用：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-518">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f3b1a-519">在 Razor 外部，始终对用户请求中的文件名内容执行 <xref:System.Net.WebUtility.HtmlEncode*>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-519">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f3b1a-520">许多实现都必须包含关于文件是否存在的检查；否则文件会被使用相同名称的文件覆盖。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-520">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f3b1a-521">提供其他逻辑以符合应用的规范。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-521">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f3b1a-522">大小验证</span><span class="sxs-lookup"><span data-stu-id="f3b1a-522">Size validation</span></span>

<span data-ttu-id="f3b1a-523">限制上传的文件的大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-523">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f3b1a-524">在示例应用中，文件大小限制为 2 MB（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-524">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f3b1a-525">通过 appsettings.json 文件中的[配置](xref:fundamentals/configuration/index)提供此限制： </span><span class="sxs-lookup"><span data-stu-id="f3b1a-525">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f3b1a-526">将 `FileSizeLimit` 注入到 `PageModel` 类：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-526">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f3b1a-527">文件大小超出限制时，将拒绝文件：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-527">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f3b1a-528">使名称属性值与 POST 方法的参数名称匹配</span><span class="sxs-lookup"><span data-stu-id="f3b1a-528">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f3b1a-529">在发布窗体数据或直接使用 JavaScript `FormData` 的非 Razor 窗体中，窗体元素或 `FormData` 指定的名称必须与控制器操作的参数名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-529">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f3b1a-530">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-530">In the following example:</span></span>

* <span data-ttu-id="f3b1a-531">使用 `<input>` 元素时，将 `name` 属性设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-531">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f3b1a-532">使用 JavaScript `FormData` 时，将名称设置为值 `battlePlans`：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-532">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f3b1a-533">将匹配的名称用于 C# 方法的参数 (`battlePlans`)：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-533">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f3b1a-534">对于名称为 `Upload` 的 Razor Pages 页处理程序方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-534">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f3b1a-535">对于 MVC POST 控制器操作方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-535">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f3b1a-536">服务器和应用程序配置</span><span class="sxs-lookup"><span data-stu-id="f3b1a-536">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f3b1a-537">多部分正文长度限制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-537">Multipart body length limit</span></span>

<span data-ttu-id="f3b1a-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置每个多部分正文的长度限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f3b1a-539">分析超出此限制的窗体部分时，会引发 <xref:System.IO.InvalidDataException>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-539">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f3b1a-540">默认值为 134,217,728 (128 MB)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-540">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f3b1a-541">使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 设置自定义此限制：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-541">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f3b1a-542">使用 <xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 设置单个页面或操作的 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-542"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f3b1a-543">在 Razor Pages 应用中，使用 `Startup.ConfigureServices` 中的[约定](xref:razor-pages/razor-pages-conventions)应用筛选器：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-543">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f3b1a-544">在 Razor Pages 应用或 MVC 应用中，将筛选器应用到页面模型或操作方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-544">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f3b1a-545">Kestrel 最大请求正文大小</span><span class="sxs-lookup"><span data-stu-id="f3b1a-545">Kestrel maximum request body size</span></span>

<span data-ttu-id="f3b1a-546">对于 Kestrel 托管的应用，默认的最大请求正文大小为 30,000,000 个字节，约为 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-546">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f3b1a-547">使用 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 服务器选项自定义限制：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-547">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="f3b1a-548">使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 设置单个页面或操作的 [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-548"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f3b1a-549">在 Razor Pages 应用中，使用 `Startup.ConfigureServices` 中的[约定](xref:razor-pages/razor-pages-conventions)应用筛选器：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-549">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f3b1a-550">在 Razor Pages 应用或 MVC 应用中，将筛选器应用到页面处理程序类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-550">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f3b1a-551">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-551">Other Kestrel limits</span></span>

<span data-ttu-id="f3b1a-552">其他 Kestrel 限制可能适用于 Kestrel 托管的应用：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-552">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f3b1a-553">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="f3b1a-553">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f3b1a-554">请求和响应数据速率</span><span class="sxs-lookup"><span data-stu-id="f3b1a-554">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f3b1a-555">IIS 内容长度限制</span><span class="sxs-lookup"><span data-stu-id="f3b1a-555">IIS content length limit</span></span>

<span data-ttu-id="f3b1a-556">默认的请求限制 (`maxAllowedContentLength`) 为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-556">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f3b1a-557">请在 web.config 文件中自定义此限制： </span><span class="sxs-lookup"><span data-stu-id="f3b1a-557">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f3b1a-558">此设置仅适用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-558">This setting only applies to IIS.</span></span> <span data-ttu-id="f3b1a-559">在 Kestrel 上托管时，默认情况下不会出现此行为。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-559">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f3b1a-560">有关详细信息，请参阅[请求限制 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-560">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f3b1a-561">ASP.NET Core 模块中的限制或 IIS 请求筛选模块的存在可能会将上传限制在 2 或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-561">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f3b1a-562">有关详细信息，请参阅[无法上传大小超出 2 GB 的文件 (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-562">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f3b1a-563">疑难解答</span><span class="sxs-lookup"><span data-stu-id="f3b1a-563">Troubleshoot</span></span>

<span data-ttu-id="f3b1a-564">以下是上传文件时遇到的一些常见问题及其可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-564">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f3b1a-565">部署到 IIS 服务器时出现“找不到”错误</span><span class="sxs-lookup"><span data-stu-id="f3b1a-565">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f3b1a-566">以下错误表示上传的文件超过服务器配置的内容长度：</span><span class="sxs-lookup"><span data-stu-id="f3b1a-566">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f3b1a-567">有关提高此限制的详细信息，请参阅 [IIS 内容长度限制](#iis-content-length-limit)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-567">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f3b1a-568">连接失败</span><span class="sxs-lookup"><span data-stu-id="f3b1a-568">Connection failure</span></span>

<span data-ttu-id="f3b1a-569">连接错误和重置服务器连接可能表示上传的文件超出 Kestrel 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-569">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f3b1a-570">有关详细信息，请参阅 [Kestrel 最大请求正文大小](#kestrel-maximum-request-body-size)部分。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-570">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f3b1a-571">可能还需要调整 Kestrel 客户端连接限制。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-571">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f3b1a-572">IFormFile 的空引用异常</span><span class="sxs-lookup"><span data-stu-id="f3b1a-572">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f3b1a-573">如果控制器正在接受使用 <xref:Microsoft.AspNetCore.Http.IFormFile> 上传的文件，但该值为 `null`，请确认 HTML 窗体指定的 `multipart/form-data` 值是否为 `enctype`。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-573">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f3b1a-574">如果未在 `<form>` 元素上设置此属性，则不会发生文件上传，并且任何绑定的 <xref:Microsoft.AspNetCore.Http.IFormFile> 参数都为 `null`。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-574">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f3b1a-575">此外，请确认[窗体数据中的上传命名是否与应用的命名相匹配](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="f3b1a-575">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="f3b1a-576">其他资源</span><span class="sxs-lookup"><span data-stu-id="f3b1a-576">Additional resources</span></span>

* <span data-ttu-id="f3b1a-577">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)（不受限制的文件上传）</span><span class="sxs-lookup"><span data-stu-id="f3b1a-577">[Unrestricted File Upload](https://www.owasp.org/index.php/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="f3b1a-578">Azure 安全性：安全框架：输入验证 | 缓解措施</span><span class="sxs-lookup"><span data-stu-id="f3b1a-578">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="f3b1a-579">Azure 云设计模式：附属密钥模式</span><span class="sxs-lookup"><span data-stu-id="f3b1a-579">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
